---
title: Use Golang to develop web service-2
date: 2019-10-21 23:15:55
tags: golang
categories: golang
---

opensource software 

github.com/spf13/viper


# Load configuration file

viper is configuration framework and it's widely used in other opensource golang software such as hugo, kubernets.

```
go get "github.com/spf13/viper"
```


## load json format


config file is like

```
json config example,
{
  "server": "127.0.0.1:4000",
  "appName": "autotesting"
}
```

```

//with following two lines, viper will load config.json file
//and parse the file
viper.SetConfigFile("$PATH/go/autoproxy/config.json")
viper.ReadInConfig()

server := viper.Get("server")
fmt.Println(server)
```

## nested config with json format

nested json config example

```
{
  "server": "127.0.0.1:4000",
  "appName": "autotesting",
  "nestedA": {
    "addr": "127.0.0.1:6379",
    "password": "test1234"
  }
}
```

```
	//code example to load config
	nestedA := viper.GetStringMap("nestedA")
	fmt.Println(nestedA["addr"].(string))
	fmt.Println(nestedA["password"].(string))
```

# load yaml format


config.yaml config example

```
environments:
	dev:
        server: http://dev.bar.com
        name: Developer Setup
    prod:
        url: http://foo.bar.com
        name: My Cool App

```

	viper.SetConfigFile("$PATH/config.yaml")
	viper.ReadInConfig()
	
	//get the environments part
	server := viper.Get("environments").(map[string]interface {})
	
	
	//get the dev part
	dev := server["dev"].(map[string]interface{})
	fmt.Println("reading server:", dev["server"].(string))
```


