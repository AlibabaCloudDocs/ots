# Delete tunnels

You can call the DeleteTunnel operation to delete a tunnel for a table by specifying the name of the table and tunnel.

## Request parameters

|Parameter|Description|
|---------|-----------|
|TableName|The name of the table for which you want to delete the tunnel.|
|TunnelName|The tunnel name.|

## Response parameters

|Parameter|Description|
|---------|-----------|
|ResponseInfo|Some other fields returned, including the RequestId in the request. RequestId uniquely identifies the request. |

## Examples

```
req := &tunnel.DeleteTunnelRequest {
   TableName: "testTable",
   TunnelName: "testTunnel",
}
_, err := tunnelClient.DeleteTunnel(req)
if err != nil {
   log.Fatal("delete test tunnel failed", err)
}
```

