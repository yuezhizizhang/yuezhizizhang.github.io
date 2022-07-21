---
layout: post
title:  "Use OpenAPI Generator to auto-generate ASP.NET Core Web API"
categories: "git ssh"
---

In this post, I will explain how to utilize OpenAPI Generator tool to auto-generate the ASP.NET Core Web API controllers and models from an OpenAPI YAML specification.

### Generate ASP.NET Core controllers and models

Let's take [petstore.yaml](https://raw.githubusercontent.com/openapitools/openapi-generator/master/modules/openapi-generator/src/test/resources/3_0/petstore.yaml){:target="_blank"} as an example. To generate ASP.NET Core Web API, I will run OpenAPI Generator commands via Docker on my Windows 10 machine.

```cmd
docker run --rm ^
-v /C/Users/fiona/Petstore:/local openapitools/openapi-generator-cli generate ^
-i /local/petstore.yaml ^
-g aspnetcore ^
-c /local/config.json ^
-o /local/server
```

Firstly, I created a directory "C:\Users\fiona\Petstore", copied [petstore.yaml](https://raw.githubusercontent.com/openapitools/openapi-generator/master/modules/openapi-generator/src/test/resources/3_0/petstore.yaml){:target="_blank"} into it. Then I mounted my Windows directory "C:\Users\fiona\Petstore" to directory "/local" in the container. The Docker image "openapitools/openapi-generator-cli" comes from [https://hub.docker.com/r/openapitools/openapi-generator-cli](https://hub.docker.com/r/openapitools/openapi-generator-cli){:target="_blank"}. 

```cmd
-v /C/Users/fiona/Petstore:/local openapitools/openapi-generator-cli generate
```

Because "C:\Users\fiona\Petstore" is mapped to "/local" in the container, I pointed to the yaml file using "/local/petstore.yaml".

```cmd
-i /local/petstore.yaml
```

OpenAPI Generator is able to generate all kinds of clients and servers. Please refer to [Generators List](https://openapi-generator.tech/docs/generators/){:target="_blank"} to learn more.

```cmd
-g aspnetcore
```

Please refer to [ASP.NET Core Config Options](https://openapi-generator.tech/docs/generators/aspnetcore/){:target="_blank"} to see what options you can config.

```cmd
-c /local/config.json
```

An example of configuration options are as below: 

```json
{
  "packageName": "Petstore.API",
  "packageTitle": "Pet Store",
  "aspnetCoreVersion": "6.0",
  "enumNameSuffix": "",
  "enumValueSuffix": "",
  "operationIsAsync": true,
  "operationResultTask": true
}
```

Finally we specify the output directory.

```cmd
-o /local/server
```



### Customize Templates

From time to time, we may want to tweak the templates a little bit. For example, we noticed that the generated **Async** methods are not named with **Async** suffix. To fix it, what we can do is:

Firstly, we downloaded the controller template [controller.mustache](https://github.com/OpenAPITools/openapi-generator/blob/master/modules/openapi-generator/src/main/resources/aspnetcore/3.0/controller.mustache){:target="_blank"}. You might be new to Mustache templates. I think the best resource to quickly wrap it up is the [Mustache Manual](https://mustache.github.io/mustache.5.html){:target="_blank"}.

Next we copied the Mustache template to a directory named "templates" under "C:\Users\fiona\Petstore". 

Finally, we modified the template, adding "Async" suffix to the method name when the method is async.

![tempate](/assets/2022-07-19-openapi-generator-template.png "template")

To use the customized templates in the generator, please add:

```cmd
-t /local/templates
```