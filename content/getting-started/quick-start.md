---
title: Quick Start
linktitle: Quick Start
description: Create a API Gatway with Arbor
date: 2013-07-01
publishdate: 2013-07-01
categories: [getting started]
keywords: [quick start,usage]
authors: ["@narendasan"]
menu:
    docs:
        parent: "getting-started"
        weight: 10
weight: 10
sections_weight: 10
draft: false
aliases: [/quickstart/,/overview/quickstart/]
toc: true
---

{{% note %}}
For instructions extenisive instructions on how to install Arbor, see [install](/getting-started/installing).

You also need [Git installed](https://git-scm.com/downloads) to run this tutorial.
{{% /note %}}

## Step 1: Install Arbor
Start by creating a project to host your gateway, typically this means creating a directory ```[GOHOME]/src/[project namespace]/[my_api_gateway]``` and intializing a git repo. 

Then intialize dependecy managment using ```dep init```.
Finally add arbor to your project using:

```
dep ensure -add github.com/arbor-dev/arbor
```

## Step 2: Boilerplate

There are a couple files and directories that you should create right away

### ```main.go```
```main.go``` is the main file of your gateway (duh). Its going to be the main point of interface 
between your service specifications and the Arbor Server. 

A boilerplate ```main.go``` would be as follows:

```go
package main

import (
	"github.com/arbor-dev/arbor"
    "github.com/[MY_NAMESPACE]/api-gateway/services"
    "github.com/[MY_NAMESPACE]/api-gateway/config"
)

func main() {
	//Configure Arbor
	config.LoadConfigArbor()
	//Register the Routes in a Collection and Boot Arbor
	arbor.Boot(services.RegisterRoutes(), "0.0.0.0", 8000)
}
```

As you can see the ```main.go``` is pretty simple, all it does is load the arbor configurations, then boot the server
by passing in the routes, the hostname and port to serve on. We use a function in the services package of your API Gateway project to assemble the set of routes to serve (more on this later).

### ```services```

The Services direction is the place to put your Service Definitions, basically the endpoints any client will be able to hit. 

#### ```services/services.go```

We recommend that the first thing you do is create a file in the services package that will aggregate all of the routes you define into a single collection of routes. We tend to also define a index route here too that can just serve as an easy way to make sure the gateway is up.

Here is an example of a ```services.go```:
```go
package services 

import (
    "fmt"
    "net/http"
    "github.com/arbor-dev/arbor"
)

//A Index Route to see if the Gateway is up
var routes = arbor.RouteCollection {
    arbor.route {
        Name: "Index",
        Method: "GET",
        Pattern: "/",
        Handler: index,
    }
}

//A Handler for the Route
func index(w http.ResponseWritter, r *http.Request) {
    fmt.Fprint(w, "Welcome!\n")
}

// A Function (called by main) which will aggregate
// all the routes into a single collection
func RegisterRoutes() arbor.RouteCollection {
    routes = append(routes, MyExampleServiceDefinition)
    return routes
} 
```

#### ```services/myexampleservice.go```

A service definition has a relatively standard format:

You start with importing arbor and any configurations (more on this later)
```go
package services 

import (
    "net/http"
    "github.com/arbor-dev/arbor"
    "github.com/[MY_NAMESPACE]/api-gateway/config"
)
```

Then you declare the endpoint for the service, and the request format.

```go
//URL of the Product Service API
const productServiceURL string = "http://0.0.0.0:5000"

//Data format of the API
const productServiceFormat string = "JSON"
```

Then you layout the endpoints of the service.

```go
//Collection of routes to expose
var productServiceRoutes = arbor.RouteCollection {
	//Route definition
	arbor.Route{
		Name:    "GetProducts",
		Method:  "GET",
		Pattern: "/products",
		Handler: getProducts,
	},
	arbor.Route{
		Name:    "CreateProduct",
		Method:  "POST",
		Pattern: "/product",
		Handler: createProduct,
	},
	arbor.Route{
		Name:    "GetProduct",
		Method:  "GET",
		Pattern: "/products/{id:[0-9]+}",
		Handler: getProduct,
	},
	arbor.Route{
		Name:    "UpdateProduct",
		Method:  "PUT",
		Pattern: "/products/{id:[0-9]+}",
		Handler: updateProduct,
	},
	arbor.Route{
		Name:    "DeleteProduct",
		Method:  "DELETE",
		Pattern: "/products/{id:[0-9]+}",
		Handler: deleteProduct,
	},
}
```

Finally, we define the proxy handlers for each route, this is the chance for you to insert any middleware you
want between when the request hits the gateway and before it goes out to the service. 

```go 
//Handlers
func getProducts(w http.ResponseWriter, r *http.Request) {
	arbor.GET(w, productServiceURL+r.URL.String(), productServiceFormat, "", r)
}

func createProduct(w http.ResponseWriter, r *http.Request) {
	arbor.POST(w, productServiceURL+r.URL.String(), productServiceFormat, "", r)
}

func getProduct(w http.ResponseWriter, r *http.Request) {
	arbor.GET(w, productServiceURL+r.URL.String(), productServiceFormat, "", r)
}

func updateProduct(w http.ResponseWriter, r *http.Request) {
	arbor.PUT(w, productServiceURL+r.URL.String(), productServiceFormat, "", r)
}

func deleteProduct(w http.ResponseWriter, r *http.Request) {
	arbor.DELETE(w, productServiceURL+r.URL.String(), productServiceFormat, "", r)
}
```

And that completes a service definition. Here is the complete definition: 

```go 
package gateway

import (
	"net/http"
	"github.com/arbor-dev/arbor"
)
//URL of the Product Service API
const productServiceURL string = "http://0.0.0.0:5000"

//Data format of the API
const productServiceFormat string = "JSON"

//Collection of routes to expose
var productServiceRoutes = arbor.RouteCollection {
	//Route definition
	arbor.Route{
		Name:    "GetProducts",
		Method:  "GET",
		Pattern: "/products",
		Handler: getProducts,
	},
	arbor.Route{
		Name:    "CreateProduct",
		Method:  "POST",
		Pattern: "/product",
		Handler: createProduct,
	},
	arbor.Route{
		Name:    "GetProduct",
		Method:  "GET",
		Pattern: "/products/{id:[0-9]+}",
		Handler: getProduct,
	},
	arbor.Route{
		Name:    "UpdateProduct",
		Method:  "PUT",
		Pattern: "/products/{id:[0-9]+}",
		Handler: updateProduct,
	},
	arbor.Route{
		Name:    "DeleteProduct",
		Method:  "DELETE",
		Pattern: "/products/{id:[0-9]+}",
		Handler: deleteProduct,
	},
}

//Handlers
func getProducts(w http.ResponseWriter, r *http.Request) {
	arbor.GET(w, productServiceURL+r.URL.String(), productServiceFormat, "", r)
}

func createProduct(w http.ResponseWriter, r *http.Request) {
	arbor.POST(w, productServiceURL+r.URL.String(), productServiceFormat, "", r)
}

func getProduct(w http.ResponseWriter, r *http.Request) {
	arbor.GET(w, productServiceURL+r.URL.String(), productServiceFormat, "", r)
}

func updateProduct(w http.ResponseWriter, r *http.Request) {
	arbor.PUT(w, productServiceURL+r.URL.String(), productServiceFormat, "", r)
}

func deleteProduct(w http.ResponseWriter, r *http.Request) {
	arbor.DELETE(w, productServiceURL+r.URL.String(), productServiceFormat, "", r)
}
```



### ```config``` and ```config/config.go```
