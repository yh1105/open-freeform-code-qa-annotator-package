
# Post \#65608610 [Link](https://stackoverflow.com/questions/65608610/)

## How to use gin as a server to write prometheus exporter metrics

**Vote**: 8 (428/702) **Views**: 7095 (482/702) 

**Internal ID** \#2-8-466

Created at 2021-01-07 08:02:21

Tags: `go` `prometheus` `go-gin`

----------

#### Metadata:

Area: `Back-end`

Language: `go` (full parsed tag list: `go`)

----------

**Notepad**


----------

This is the official prometheus golang-client example：
```
package main

import (
    "log"
    "net/http"

    "github.com/prometheus/client_golang/prometheus"
    "github.com/prometheus/client_golang/prometheus/promhttp"
)

var cpuTemp = prometheus.NewGauge(prometheus.GaugeOpts{
        Name: "cpu_temperature_celsius",
        Help: "Current temperature of the CPU.",
    })


func init() {
    // Metrics have to be registered to be exposed:
    prometheus.MustRegister(cpuTemp)
}

func main() {
    cpuTemp.Set(65.3)

    // The Handler function provides a default handler to expose metrics
    // via an HTTP server. "/metrics" is the usual endpoint for that.
    http.Handle("/metrics", promhttp.Handler())
    log.Fatal(http.ListenAndServe(":8080", nil))
}
```

In this code, the http server uses the `promhttp` library.
How to modify the metrics handler when using the `gin` framework? I did not find answers in the [documentation](https://pkg.go.dev/github.com/prometheus/client_golang/prometheus).


----------
        
## Answer \#0

**Accepted** Vote: 19

Created at 2021-01-07 08:36:34

------------

We just utilize `promhttp` handler.
```
package main

import (
    "github.com/gin-gonic/gin"
    "github.com/prometheus/client_golang/prometheus"
    "github.com/prometheus/client_golang/prometheus/promhttp"
)

var cpuTemp = prometheus.NewGauge(prometheus.GaugeOpts{
    Name: "cpu_temperature_celsius",
    Help: "Current temperature of the CPU.",
})

func init() {
    prometheus.MustRegister(cpuTemp)
}

func prometheusHandler() gin.HandlerFunc {
    h := promhttp.Handler()

    return func(c *gin.Context) {
        h.ServeHTTP(c.Writer, c.Request)
    }
}

func main() {
    cpuTemp.Set(65.3)

    r := gin.New()

    r.GET("/", func(c *gin.Context) {
        c.JSON(200, "Hello world!")
    })

    r.GET("/metrics", prometheusHandler())

    r.Run()
}
```

Or we always can switch to Prometheus middleware - [https://github.com/zsais/go-gin-prometheus](https://github.com/zsais/go-gin-prometheus)


------------
    
    
## Answer \#1

 Vote: 8

Created at 2022-02-13 20:27:11

------------

Use gin wrapper
```
router.GET("/metrics", gin.WrapH(promhttp.Handler()))
```



------------
    
    
## Answer \#2

 Vote: 2

Created at 2021-01-14 04:51:43

------------

I am using prometheus with other library [https://github.com/Depado/ginprom](https://github.com/Depado/ginprom):
```
package main

import (
    "github.com/Depado/ginprom"
    "github.com/gin-gonic/gin"
)

func main() {
    r := gin.Default()
    p := ginprom.New(
        ginprom.Engine(r),
        ginprom.Subsystem("gin"),
        ginprom.Path("/metrics"),
    )
    r.Use(p.Instrument())

    r.GET("/hello/:id", func(c *gin.Context) {})
    r.GET("/world/:id", func(c *gin.Context) {})
    r.Run("127.0.0.1:8080")
}
```



------------
    
    