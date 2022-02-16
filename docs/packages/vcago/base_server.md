---
layout: default
title: BasicWebserver
parent: VcAgo
nav_order: 2
has_children: false
---

# Basic Webserver


## Setup in server.go

```
func main() {
    e := echo.New()
    e.HTTPErrorHandler = vcago.HTTPErrorHandler    
    e.Validator = vcago.JSONValidator
    e.Use(vcago.CORS.Init())
    e.Use(vcago.Logger.Init()) 
    ...

    ...
    appPort := vcago.Config.GetEnvString("APP_PORT", "n", "1323")
    e.Logger.Fatal(e.Start(":" + appPort))
}
```
## edit the .env file

```
SERVICE_NAME=default  //service name, default default
APP_PORT=1323           // default port 1323
LOGGING_OUTPUT=strout  // pretty, nats default strout
ALLOW_ORIGINS="https://example.com,https://api.example.com"
...
```

## output logger pretty

```
{
    "id": "",  //not implemented
    "service": "example",
    "time": "2022-02-07T19:50:08.971851362+01:00",
    "remote_ip": "127.0.0.1",
    "host": "localhost:1323",
    "method": "POST",
    "uri": "/example",
    "user_agent": "Mozilla/5.0 (X11; Linux x86_64; rv:96.0) Gecko/20100101 Firefox/96.0",
    "status": 500,
    "error": {
        "status_message": "example error",
        "status_type": "internal_error"
    },
    "latency": 2002915,
    "latency_human": "2.002915ms",
    "byte_in": "",
    "byte_out": "",
    "modified": {
        "updated": 1644259808,
        "created": 1644259808
    }
}
```
