---
layout: post
title:  "Swagger Editor Auto-complete"
categories: "OpenAPI Swagger Editor"
---

I've been working on some Microservices projects recently. Each Microservices component exposes a set of services via REST API. The [OpenAPI specification](https://spec.openapis.org/oas/v3.0.0){:target="_blank"} is a standard interface to REST APIs.

In our development, we adopt the API design first approach, which involves design and define the interface first, ahead of any service implementations. So the OpenAPI YAML file becomes the cornerstone of the project.

However when I read our OpenAPI YAML files, I felt it's not defined in a very consistent way. Take an example, Models are defined using the Schema object. <code class="code">type</code> property is used to specify the data type. In addition to <code class="code">type</code> property, we may add a <code class="code">format</code> property to provide supplementary detail to the data type. <code class="code">format</code> can be int32, int64, float, double, byte, date, email, uuid, and etc. But <code class="code">format</code> property is optional. Consequently, some types have format, but some don't even though they should have.

```yaml
schema:
    type: string
    format: uuid
```

I wouldn't blame the developers for it. In my opinion, nobody needs to memorize OpenAPI specification. Essentially, OpenAPI is merely a contract to define:
1. available endpoints (Paths)
2. operations on the endpoints (Methods get/post/put/delete)
3. request parameters, response status and body

Let's consider it from the business perspective. Does it matter if an operation should have a <code class="code">operationId</code> property or not? Does it matter if the data types should be specified under <code class="code">schema</code> or not? If the answers is **NO**, then what's the point of memorizing the schema specification? Can't the OpenAPI editor help us with it?

Sadly speaking, the prompt or auto-complete capability of Swagger editor is very limited. Before I proceed to where we can enhance it, some people may not even know how to run Swagger editor locally. To run it locally, clone the [Swagger editor](https://github.com/swagger-api/swagger-editor){:target="_blank"} project from GitHub. To build and run it:

```cmd
# Install npm packages (if needed)
npm install

# Build the app
npm run build

# Start the app
npm run start
```

To open the prompt hint in Swagger editor, press <code class="code">ctrl + space</code> key. In the same way, you can open the command prompt in the [Swagger Editor Online](https://editor.swagger.io/){:target="_blank"}.

![Cascading Selector](/assets/2021-10-08-swagger-editor-snippet.png "swagger editor snippet prompt")

The auto suggest snippet is defined in [snippet.js](https://github.com/swagger-api/swagger-editor/blob/master/src/plugins/editor-autosuggest-snippets/snippets.js){:target="_blank"}. To make the snippet more concrete, you can update the method <code>makeOperationSnippet</code> to provide a more detailed snippet for the operations. For instance, if you are working with OpeanAPI 3.0, probably you can update the method as:

```JavaScript
function makeOperationSnippet(operationName) {
  return [
    "${1:" + operationName + "}:",
    "  summary: ${2}",
    "  description: >-",
    "    ${2:description}",
    "  operationId: ${3}",
    "  parameters: ${4}",
    "    - name: ${5:parameter_name}",
    "      in: ${6:query}",
    "      required: true",
    "      description: ${7:description}",
    "      schema:",
    "        type: ${8:string}",
    "        description: ${9:description}",
    "        format: ${10:uuid}",
    "  requestBody:",
    "    content:",
    "      application/json:",
    "        schema:",
    "          \$ref:",
    "    description: ${12:description}",
    "    required: true",
    "  responses:",
    "    ${13:200:}",
    "      description: ${14:OK}",
    "      content:",
    "        application/json:",
    "          schema:",
    "            \$ref:",
    "${10}"
  ].join("\n")
}
```

Thus, the autocomplete is able to provide you with a much more comprehensive snippet to work on. Your remaining work becomes much simpler. You only have to fill in the fields with proper values.