# Configuration items

This topic describes the configuration items of Tunnel Service.

## tunnel client

When you initialize the Tunnel Service client, you can customize client configurations by using the NewTunnelClientWithConfig operation. If the config parameter is not specified or is set to nil, DefaultTunnelConfig is used.

```
var DefaultTunnelConfig = &TunnelConfig{
      // Set the maximum exponential backoff value to retry a failed request.
      MaxRetryElapsedTime: 45 * time.Second,
      // Set the maximum timeout period for the server to receive an HTTP request.
      RequestTimeout:      30 * time.Second,
      // Specify http.DefaultTransport.
      Transport:           http.DefaultTransport,
}
```

## Worker

The TunnelWorkerConfig operation contains the configurations required for a worker. The ProcessorFactory field is required. If other parameters are unspecified, their default values are used.

```
type TunnelWorkerConfig struct {
   // The heartbeat timeout periods for the worker and Tunnel Service are the same. The default value is retained.
   HeartbeatTimeout  time.Duration
   // Set the frequency at which worker sends heartbeat information.
   HeartbeatInterval time.Duration
   // Set the operation that is used to establish connections for consumption of data exported by using Tunnel Service. The default value is retained.
   ChannelDialer     ChannelDialer

   // Generate the specific processor for the connection. The callback function is typically used to initialize the SimpleProcessFactory operation.
   ProcessorFactory ChannelProcessorFactory

   // Configure ZAP logs. The default value is DefaultLogConfig.
   LogConfig      *zap.Config
   // Configure log rotation for ZAP. The default value is DefaultSyncer.
   LogWriteSyncer zapcore.WriteSyncer
}
```

ProcessorFactory is the operation that is used to configure information such as the callback function information. We recommend that you use the SimpleProcessorFactory operation that is defined in Tunnel Service SDK for GO:

```
type SimpleProcessFactory struct {
   // Define and configure parameters. User-defined information is passed to the ChannelContext parameter contained in ProcessFunc and ShutdownFunc operations.
   CustomValue interface{}

   // Set the interval to receive information of two consecutive checkpoints. The worker records the interval. When CpInterval is set to a value smaller than or equal to 0, DefaultCheckpointInterval is used.
   CpInterval time.Duration

   // Set the synchronous callback function for the worker to process data. If an error is returned from ProcessFunc, the worker sends retry requests to ProcessFunc to obtain the data based on exponential backoff.
   ProcessFunc  func(channelCtx *ChannelContext, records []*Record) error
   // Set the synchronous callback function that is used when the worker exits.
   ShutdownFunc func(channelCtx *ChannelContext)

   // Set logs. If the Logger field is set to nil, DefaultLogConfig is used to initialize the Logger field.
   Logger *zap.Logger
}
```

## Logs

The following examples describe the default configurations of logs and log rotation:

-   Default configurations of logs:

    ```
    // By default, DefaultLogConfig is used in the TunnelWorkerConfig and SimpleProcessFactory operations to configure logs.
    var DefaultLogConfig = zap.Config{
       Level:       zap.NewAtomicLevelAt(zap.InfoLevel),
       Development: false,
       Sampling: &zap.SamplingConfig{
          Initial:    100,
          Thereafter: 100,
       },
       Encoding: "json",
       EncoderConfig: zapcore.EncoderConfig{
          TimeKey:        "ts",
          LevelKey:       "level",
          NameKey:        "logger",
          CallerKey:      "caller",
          MessageKey:     "msg",
          StacktraceKey:  "stacktrace",
          LineEnding:     zapcore.DefaultLineEnding,
          EncodeLevel:    zapcore.LowercaseLevelEncoder,
          EncodeTime:     zapcore.ISO8601TimeEncoder,
          EncodeDuration: zapcore.SecondsDurationEncoder,
          EncodeCaller:   zapcore.ShortCallerEncoder,
       },
    }
    ```

-   Default configurations of log rotation:

    ```
    // By default, DefaultSyncer is used in the TunnelWorkerConfig and SimpleProcessFactory operations to configure log rotation.
    var DefaultSyncer = zapcore.AddSync(&lumberjack.Logger{
       // Set the path of logs.
       Filename:   "tunnelClient.log",
       // Set the maximum size of each log.
       MaxSize:    512, //MB
       // Set the maximum number of backups that can be compressed for each rotated log.
       MaxBackups: 5,
       // Set the maximum number of days for which logs can be retained.
       MaxAge:     30, //days
       // Specify whether to compress the rotated logs.
       Compress:   true,
    })
    ```


