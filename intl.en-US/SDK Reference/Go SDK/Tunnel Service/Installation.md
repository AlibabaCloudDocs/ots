# Installation

This topic describes how to install Tunnel Service.

## Download the source code package

```
go get github.com/aliyun/aliyun-tablestore-go-sdk/tunnel
```

## Install dependencies

-   You can go to the tunnel directory and use the dep tool to install the dependencies.
    -   Install the [dep](https://github.com/golang/dep#installation) tool.
    -   dep ensure -v
-   You can also run the go get command to install the dependencies:

    ```
    go get -u go.uber.org/zap
    go get github.com/cenkalti/backoff
    go get github.com/golang/protobuf/proto
    go get github.com/satori/go.uuid
    go get github.com/stretchr/testify/assert
    go get github.com/smartystreets/goconvey/convey
    go get github.com/golang/mock/gomock
    go get gopkg.in/natefinch/lumberjack.v2
    ```


