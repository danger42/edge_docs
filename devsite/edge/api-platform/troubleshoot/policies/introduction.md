{% extends "_base.html" %} {% block title %}Introduction to Policy Playbooks{% endblock %} {% block body %}

The policy playbooks provide detailed troubleshooting information for runtime and
deployment errors associated with Edge policies.

## Playbooks for policy runtime errors

Runtime errors are errors that can occur while a policy executes in the context
of an API proxy flow. When a runtime error occurs, the proxy flow enters the error
state, and you need to diagnose and fix the problem and redeploy the proxy.

Apigee currently provides policy runtime error playbooks for these policies:

* [Assign message](/api-platform/troubleshoot/policies/runtime/assign-message-runtime-errors)
* [Basic authentication](/api-platform/troubleshoot/policies/runtime/basic-authentication-runtime-errors)
* [Extract variables](/api-platform/troubleshoot/policies/runtime/extract-variables-runtime-errors)
* [Flow callout](/api-platform/troubleshoot/policies/runtime/flowcallout-runtime-errors)
* [JavaScript](/api-platform/troubleshoot/policies/runtime/javascript-runtime-errors)
* [JSONThreatProtection](/api-platform/troubleshoot/policies/runtime/jsonthreatprotection-errors)
* [JSON to XML](/api-platform/troubleshoot/policies/runtime/jsontoxml-runtime-errors)
* [Key Value Map Operations](/api-platform/troubleshoot/policies/runtime/key-value-map-runtime-errors)
* [Quota](/api-platform/troubleshoot/policies/runtime/quota-runtime-errors)
* [Reset Quota](/api-platform/troubleshoot/policies/runtime/reset-quota-runtime-errors)
* [Service callout](/api-platform/troubleshoot/policies/runtime/service-callout-runtime-errors)
* [Spike arrest](/api-platform/troubleshoot/policies/runtime/spike-arrest-runtime-errors)
* [XMLThreatProtection](/api-platform/troubleshoot/policies/runtime/xmlthreatprotection-runtime-errors)
* [XML to JSON](/api-platform/troubleshoot/policies/runtime/xmltojson-runtime-errors)

## Playbooks for policy deployment errors

Deployment errors occur when you deploy your proxy and Edge determines a policy
is not configured correctly. Deployment stops, and you need to debug and
fix the problem before you can deploy the proxy.

Apigee currently provides policy deployment error playbooks for these policies:

* [Assign message](/api-platform/troubleshoot/policies/deployment/assign-message-deployment-errors)
* [BasicAuthentication](/api-platform/troubleshoot/policies/deployment/basic-authentication-deployment-errors)
* [Extract variables](/api-platform/troubleshoot/policies/deployment/extract-variables-deployment-errors)
* [Invalidate Cache](/api-platform/troubleshoot/policies/deployment/invalidate-cache-deployment-errors)
* [JavaScript](/api-platform/troubleshoot/policies/deployment/javascript-deployment-errors)
* [Key Value Map Operations](/api-platform/troubleshoot/policies/deployment/key-value-map-deployment-errors)
* [Lookup Cache](/api-platform/troubleshoot/policies/deployment/lookup-cache-deployment-errors)
* [Message Logging](/api-platform/troubleshoot/policies/deployment/message-logging-deployment-errors)
* [Populate Cache](/api-platform/troubleshoot/policies/deployment/populate-cache-deployment-errors)
* [Quota](/api-platform/troubleshoot/policies/deployment/quota-deployment-errors)
* [Reset Quota](/api-platform/troubleshoot/policies/deployment/reset-quota-deployment-errors)
* [Response Cache](/api-platform/troubleshoot/policies/deployment/responsecache-deployment-errors)
* [Service callout](/api-platform/troubleshoot/policies/deployment/service-callout-deployment-errors)
* [Spike arrest](/api-platform/troubleshoot/policies/deployment/spike-arrest-deployment-errors)
* [Statistics collector](/api-platform/troubleshoot/policies/deployment/stats-collector-deployment-errors)
* [XML to JSON](/api-platform/troubleshoot/policies/deployment/xmltojson-deployment-errors)
* [XSL Transform](/api-platform/troubleshoot/policies/deployment/xsl-transform-deployment-errors)

{% endblock %}
