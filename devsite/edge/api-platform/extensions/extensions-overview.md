{% extends "_base.html" %}
{% block title %}What are extensions?{% endblock %}
{% block body %}

{{ extensions_availability }}

You can use extensions to integrate external resources into your API proxies. For example, you could integrate Google Cloud Platform services such as Google Cloud Storage. At run time, an API proxy uses the extension to exchange requests and responses with the external resource.

## Extension runtime framework

At runtime, an extension acts as a go-between, processing requests and responses between an API proxy and an external resource.

<img src="/api-platform/images/extensions-diagram.png" width="70%" />

Here's an overview of how processing through an extension works at runtime.

1.  A **client request** reaches an **API proxy** and its request flow proceeds through the proxy's policies.
1.  The request flow reaches a **Extension Callout policy** in the proxy that is configured to pass data to an extension deployed in the environment. The data includes values that will be needed to send a request to the external resource the extension represents.
1.  The **extension** sends a request to the **external resource**, then receives a response.
1.  The extension passes its response back to the Extension Callout policy, where it is handled by proxy code.
1.  Request flow continue through the proxy until it returns a response to the client.

## Working with extensions

Each extension is developed and configured with a particular external resource in mind. To use an extension, an Apigee Edge administrator and API proxy developer follow these high-level steps:

- An Apigee Edge **administrator** configures and deploys the extension. They configurer it with values specific to the needs of the business. This configuration creates an extension instance focused for use by the API proxy developer. For example, they might configure an extension to access a particular bucket in [Google Cloud Storage](/api-platform/reference/extensions/google-cloud-storage-extension), including required authentication credentials.
- An API **proxy developer** uses a configured extension in an API proxy by adding a [Extension Callout policy](/api-platform/reference/policies/extension-callout-policy) to the proxy. The policy's configuration specifies
  - which configured extension to use.
  - the extension actions (think of these as function calls to the extension) to invoke.
  - the action parameters to pass with the action call.
  At run time, the policy uses the extension to access the external resource as part of the proxy's flow.

## Documentation about extensions

The following topics provide more information about extensions:

- For a hands-on introduction to extensions, see [Tutorial: Adding and using an extension](/api-platform/tutorials/add-extension/intro).
- For references on extensions included with Apigee Edge, see [Extensions reference overview](/api-platform/reference/extensions/reference-overview-extensions).
- For references on the management API you can use to add, remove, and deploy extensions, see [Extensions management API reference](https://apidocs.apigee.com/api-reference/content/extensions-management-api).

{% endblock %}

