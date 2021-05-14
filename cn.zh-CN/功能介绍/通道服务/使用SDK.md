# 使用SDK

使用SDK快速体验通道服务功能。使用前，您需要了解使用通道服务的注意事项、接口等信息。

## 注意事项

-   TunnelWorkerConfig中默认会启动读数据和处理数据的线程池。如果使用的是单台机器，当需要启动多个TunnelWorker时，建议共用一个TunnelWorkerConfig。
-   在创建全量加增量类型的Tunnel时，由于Tunnel的增量日志最多会保留7天（具体的值和数据表的Stream的日志过期时间一致），全量数据如果在7天内没有消费完成，则此Tunnel进入增量阶段会出现OTSTunnelExpired错误，导致增量数据无法消费。如果您预估全量数据无法在7天内消费完成，请及时联系表格存储技术支持或者加入钉钉群23307953（表格存储技术交流群-2）进行咨询。
-   TunnelWorker的初始化需要预热时间，该值受TunnelWorkerConfig中的heartbeatIntervalInSec参数影响，可以通过TunnelWorkerConfig中的setHeartbeatIntervalInSec方法配置，默认为30s，最小值为5s。
-   当Tunnel从全量切换至增量阶段时，全量的Channel会结束，增量的Channel会启动，此阶段会有初始化时间，该值也受TunnelWorkerConfig中的heartbeatIntervalInSec参数影响。
-   当客户端（TunnelWorker）没有被正常shutdown时（例如异常退出或者手动结束），TunnelWorker会自动进行资源的回收，包括释放线程池，自动调用用户在Channel上注册的shutdown方法，关闭Tunnel连接等。

## 接口

|接口|说明|
|--|--|
|CreateTunnel|创建一个通道。|
|ListTunnel|列举某个数据表内通道的具体信息。|
|DescribeTunnel|描述某个通道里的具体Channel信息。|
|DeleteTunnel|删除一个通道。|

## 使用

您可以使用如下语言的SDK实现通道服务。

-   [Java SDK](/cn.zh-CN/SDK 参考/Java SDK/通道服务/概述.md)
-   [Go SDK](/cn.zh-CN/SDK 参考/Go SDK/通道服务/安装.md)

## 体验通道服务

使用Java SDK最小化的体验通道服务。

1.  初始化Tunnel Client。

    ```
    //endPoint为表格存储实例的endPoint，例如https://instance.cn-hangzhou.ots.aliyuncs.com。
    //accessKeyId和accessKeySecret分别为访问表格存储服务的AccessKey的Id和Secret。
    //instanceName为实例名称。
    final String endPoint = "";
    final String accessKeyId = "";
    final String accessKeySecret = "";
    final String instanceName = "";
    TunnelClient tunnelClient = new TunnelClient(endPoint, accessKeyId, accessKeySecret, instanceName);
    ```

2.  创建新通道。

    请提前创建一张测试表或者使用已有的一张数据表。如果需要新建测试表，可以使用SyncClient中的createTable方法或者使用官网控制台等方式创建。

    ```
    //支持创建TunnelType.BaseData（全量）、TunnelType.Stream（增量）、TunnelType.BaseAndStream（全量加增量）三种类型的Tunnel。
    //如下示例为创建全量加增量类型的Tunnel，如果需创建其它类型的Tunnel，则将CreateTunnelRequest中的TunnelType设置为相应的类型。
    final String tableName = "testTable";
    final String tunnelName = "testTunnel";
    CreateTunnelRequest request = new CreateTunnelRequest(tableName, tunnelName, TunnelType.BaseAndStream);
    CreateTunnelResponse resp = tunnelClient.createTunnel(request);
    //tunnelId用于后续TunnelWorker的初始化，该值也可以通过ListTunnel或者DescribeTunnel获取。
    String tunnelId = resp.getTunnelId(); 
    System.out.println("Create Tunnel, Id: " + tunnelId);
    ```

3.  用户自定义数据消费Callback，开始自动化的数据消费。

    ```
    //用户自定义数据消费Callback，即实现IChannelProcessor接口（process和shutdown）。
    private static class SimpleProcessor implements IChannelProcessor {
        @Override
        public void process(ProcessRecordsInput input) {
            //ProcessRecordsInput中包含有拉取到的数据。
            System.out.println("Default record processor, would print records count");
            System.out.println(
                //NextToken用于Tunnel Client的翻页。
                String.format("Process %d records, NextToken: %s", input.getRecords().size(), input.getNextToken()));
            try {
                //模拟消费处理。
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
        @Override
        public void shutdown() {
            System.out.println("Mock shutdown");
        }
    }
    
    //TunnelWorkerConfig默认会启动读数据和处理数据的线程池。
    //如果使用的是单台机器，当需要启动多个TunnelWorker时，建议共用一个TunnelWorkerConfig。TunnelWorkerConfig中包括更多的高级参数。
    TunnelWorkerConfig config = new TunnelWorkerConfig(new SimpleProcessor());
    //配置TunnelWorker，并启动自动化的数据处理任务。
    TunnelWorker worker = new TunnelWorker(tunnelId, tunnelClient, config);
    try {
        worker.connectAndWorking();
    } catch (Exception e) {
        e.printStackTrace();
        worker.shutdown();
        tunnelClient.shutdown();
    }
    ```


## 配置TunnelWorkerConfig

TunnelWorkerConfig提供Tunnel Client的自定义配置，可根据实际需要配置参数，参数说明请参见下表。

|配置|参数|说明|
|--|--|--|
|Heartbeat的间隔和超时时间|heartbeatTimeoutInSec|Heartbeat的超时间隔。 默认值为300s。

当Heartbeat发生超时，Tunnel服务端会认为当前TunnelClient不可用（失活），客户端需要重新进行ConnectTunnel。 |
|heartbeatIntervalInSec|进行Heartbeat的间隔。 Heartbeat用于活跃Channel的探测、Channel状态的更新、（自动化）数据拉取任务的初始化等。

默认值为30s，最小支持配置到5s，单位为s。 |
|记录消费位点的时间间隔|checkpointIntervalInMillis|用户消费完数据后，向Tunnel服务端进行记录消费位点操作（checkpoint）的时间间隔。 默认值为5000ms，单位为ms。

**说明：**

-   因为读取任务所在机器不同，进程可能会遇到各种类型的错误。例如因为环境因素重启，需要定期对处理完的数据做记录（checkpoint）。当任务重启后，会接着上次的checkpoint继续往后做。在极端情况下，通道服务不保证传给您的记录只有一次，只保证数据至少传一次，且记录的顺序不变。如果出现局部数据重复发送的情况，需要您注意业务的处理逻辑。
-   如果希望减少在出错情况下数据的重复处理，可以增加做checkpoint的频率。但是过于频繁的checkpoint会降低系统的吞吐量，请根据自身业务特点决定checkpoint的操作频率。 |
|客户端的自定义标识|clientTag|客户端的自定义标识，可以生成Tunnel Client ID，用于区分TunnelWorker。|
|数据处理的自定义Callback|channelProcessor|用户注册的处理数据的Callback，包括process和shutdown方法。|
|数据读取和数据处理的线程池资源配置|readRecordsExecutor|用于数据读取的线程池资源。无特殊需求，建议使用默认的配置。|
|processRecordsExecutor|用于处理数据的线程池资源。无特殊需求，建议使用默认的配置。 **说明：**

-   自定义上述线程池时，线程池中的线程数要和Tunnel中的Channel数尽可能一致，此时可以保障每个Channel都能很快的分配到计算资源（CPU）。
-   在默认线程池配置中，为了保证吞吐量，表格存储进行了如下操作：
    -   默认预先分配32个核心线程，以保障数据较小时（Channel数较少时）的实时吞吐量。
    -   工作队列的大小适当调小，当在用户数据量比较大（Channel数较多）时，可以更快的触发线程池新建线程的策略，及时的弹起更多的计算资源。
    -   设置了默认的线程保活时间（默认为60s），当数据量降下后，可以及时回收线程资源。 |
|内存控制|maxChannelParallel|读取和处理数据的最大Channel并行度，可用于内存控制。 默认值为-1，表示不限制最大并行度。

**说明：** 仅Java SDK 5.10.0及以上版本支持此功能。 |
|最大退避时间配置|maxRetryIntervalInMillis|Tunnel的最大退避时间基准值，最大退避时间在此基准值附近随机变化，具体范围为0.75\*maxRetryIntervalInMillis~1.25\*maxRetryIntervalInMillis。 默认值为2000ms，最小值为200ms。

**说明：**

-   仅Java SDK 5.4.0及以上版本支持此功能。
-   Tunnel对于数据量较小的情况（单次拉取小于900 KB或500条）会进行一定时间的指数退避，直至达到最大退避时间。 |

## 附录：完整代码

```
import com.alicloud.openservices.tablestore.TunnelClient;
import com.alicloud.openservices.tablestore.model.tunnel.CreateTunnelRequest;
import com.alicloud.openservices.tablestore.model.tunnel.CreateTunnelResponse;
import com.alicloud.openservices.tablestore.model.tunnel.TunnelType;
import com.alicloud.openservices.tablestore.tunnel.worker.IChannelProcessor;
import com.alicloud.openservices.tablestore.tunnel.worker.ProcessRecordsInput;
import com.alicloud.openservices.tablestore.tunnel.worker.TunnelWorker;
import com.alicloud.openservices.tablestore.tunnel.worker.TunnelWorkerConfig;
public class TunnelQuickStart {
    private static class SimpleProcessor implements IChannelProcessor {
        @Override
        public void process(ProcessRecordsInput input) {
            System.out.println("Default record processor, would print records count");
            System.out.println(
                //NextToken用于Tunnel Client的翻页。
                String.format("Process %d records, NextToken: %s", input.getRecords().size(), input.getNextToken()));
            try {
                //模拟消费处理。
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
        @Override
        public void shutdown() {
            System.out.println("Mock shutdown");
        }
    }
    public static void main(String[] args) throws Exception {
        //1.初始化Tunnel Client。
        final String endPoint = "";
        final String accessKeyId = "";
        final String accessKeySecret = "";
        final String instanceName = "";
        TunnelClient tunnelClient = new TunnelClient(endPoint, accessKeyId, accessKeySecret, instanceName);
        //2.创建新通道（此步骤需要提前创建一张测试表，可以使用SyncClient的createTable或者使用官网控制台等方式创建）。
        final String tableName = "testTable";
        final String tunnelName = "testTunnel";
        CreateTunnelRequest request = new CreateTunnelRequest(tableName, tunnelName, TunnelType.BaseAndStream);
        CreateTunnelResponse resp = tunnelClient.createTunnel(request);
        //tunnelId用于后续TunnelWorker的初始化，该值也可以通过ListTunnel或者DescribeTunnel获取。
        String tunnelId = resp.getTunnelId();
        System.out.println("Create Tunnel, Id: " + tunnelId);
        //3.用户自定义数据消费Callback，开始自动化的数据消费。
        //TunnelWorkerConfig中有更多的高级参数。
        TunnelWorkerConfig config = new TunnelWorkerConfig(new SimpleProcessor());
        TunnelWorker worker = new TunnelWorker(tunnelId, tunnelClient, config);
        try {
            worker.connectAndWorking();
        } catch (Exception e) {
            e.printStackTrace();
            worker.shutdown();
            tunnelClient.shutdown();
        }
    }
}
```

