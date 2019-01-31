{% extends "_base.html" %}
{% block title %}Naming restrictions for {{ edge }} artifacts{% endblock %}
{% block body %}

This topic lists naming restrictions for the kinds of files and entities you can create while building API proxies.

Named Item | Maximum Characters | Allowed characters
---------- | ------------------ | ------------------
[API key](/api-platform/security/api-keys)  | 255 | Alphanumeric, space, and the following: ```_ -```
[API product](/api-platform/publish/create-api-products) | | Alphanumeric, space, and the following: ```_ - . # $ %```
[Cache name](/api-platform/cache/manage-caches-environment) | 255 | Alphanumeric, space, and the following: ```_ - .```
[Developer app](/api-platform/publish/adding-developers-your-api-product) | | Alphanumeric, space, and the following: ```_ - . # $ %```
Email ID | | Valid email address syntax
[Environment name](/api-platform/fundamentals/apigee-edge-organization-structure#aboutenvironments) | 255 | Alphanumeric, space, and the following: ```_ - .```
[Organization name](/api-platform/fundamentals/apigee-edge-organization-structure) | 255 | Alphanumeric, space, and the following: ```_ - .``` <p>For {{opdk}}, the organization name must start with an alphanumeric lowercase character, with a maximum length of 20 characters.
[Policy name (or step definition)](/api-platform/develop/attaching-and-configuring-policies-management-ui) | 255 | Alphanumeric, space, and the following: ```_ - .```
[Resource file names](/api-platform/develop/resource-files). Also used for [keystore resource names](/api-platform/system-administration/creating-keystores-and-truststore-cloud-using-edge-ui) like cert and alias. | 255 | Alphanumeric, space, and the following: ``` : / \ ! @ # $ % ^ & { } [ ] ( ) _ + - = , . ~ ' ```
[Resource file types](/api-platform/develop/resource-files). Also used for [keystore resource names](/api-platform/system-administration/creating-keystores-and-truststore-cloud-using-edge-ui) like cert and key. | 255 | Alphanumeric and the following: ```_ - .```
[Node.js script target name](/api-platform/nodejs/adding-nodejs-existing-api-proxy) | 255 | Alphanumeric and the following: ```! @ # $ % ^ & { } [ ] ( ) _ + - = , . ~ ' ```
[Revision name](/api-platform/deploy/deploy-api-proxies-using-management-api) | 5 | Numeric
URL paths | | Valid URL, excluding protocol
[User role name](/api-platform/system-administration/creating-custom-roles) | 255 | Alphanumeric and the following: ```_ - .```
[Virtual host name](/api-platform/fundamentals/configuring-virtual-hosts) | 255 | Alphanumeric, space, and the following: ```_ - .```

{% endblock %}
