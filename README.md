<p align="center">
 <img src="goconfig.png" width="450">
</p>

<div align="center">

  <a href="">![Tests](https://github.com/leonidasdeim/goconfig/actions/workflows/go.yml/badge.svg)</a>
  <a href="">![Code Scanning](https://github.com/leonidasdeim/goconfig/actions/workflows/codeql.yml/badge.svg)</a>
  <a href="">![Release](https://badgen.net/github/release/leonidasdeim/goconfig/)</a>
  <a href="">![Releases](https://badgen.net/github/releases/leonidasdeim/goconfig)</a>
  <a href="">![Contributors](https://badgen.net/github/contributors/leonidasdeim/goconfig)</a>
  
</div>

# goconfig

Config tool for Go applications that require configuration changes on-the-fly.

```bash
go get github.com/leonidasdeim/goconfig
```

## How to use

Initial config that will store configuration information should be placed in root folder named `app.default.json`. Write config structure of your app.

`goconfig` uses [validator](https://github.com/go-playground/validator) library for validating config struct. For example you can specify required configuration items with `validate:"required"` tag.

`goconfig` also let to you set up default values for entries in configuration with `default:"some value"` tag. Right now, only bool, int and string is supported.

Example of config structure:

```go
type ConfigType struct {
    Version   string `validate:"required"`
    Address   string `validate:"required,ip"`
    Prefork   bool `default:"false"`
}
```

Initialize and use config:

```go
// creates default goconfig instance with JSON file handler
c, _ := goconfig.Init[ConfigType](handler.New())

// access current configuration attributes
cfg := c.GetCfg()

// update current configuration
c.UpdateConfig(cfg)
```

If you have modules which needs to be notified on config change, add a listener/subscriber:

```go
c.AddSubscriber("name_of_subscriber")
```

Implement waiting goroutine for config change on the fly in your modules:

```go
for {
    _ = <-c.GetSubscriber("name_of_subscriber")
    reconfigureModule()
}
```

You can remove subscriber by given name on the fly as well:

```go
c.RemoveSubscriber("name_of_subscriber")
```

Library default handlers also support optional parameters with high order functions
You can specify custom path, name and file handler (currently: JSON or YAML)

```go
h := handler.New(handler.WithPath("./dir"), handler.WithName("name"), handler.WithType(handler.YAML))
c, _ := goconfig.Init[ConfigType](h)
```
