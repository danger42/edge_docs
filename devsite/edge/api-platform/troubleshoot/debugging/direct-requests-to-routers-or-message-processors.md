{% extends "_base.html" %}
{% block title %}How to make direct API requests to routers or message processors{% endblock %}
{% block body %}

Note: The techniques described in this content are available in Apigee Edge for Private Cloud only.

## Introduction

When troubleshooting, you may want to execute APIs directly against Apigee components such as a router or message processor. For example, you might want to do this in order to:

- Debug intermittent issues with certain API requests that point to an issue with a particular Apigee component (router/message processor).
- Gather more diagnostic information by enabling debug mode on a specific instance of an Apigee component.
- Rule out that the issue is caused by a specific Apigee component.
- Discover whether operations such as spawning up a new instance or restarting the instance have the desired impact.

## Prerequisites

- Direct access to the router or message processor components against which API requests need to be run.
- The [cURL](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Virtualization/3.2/html/Developer_Guide/Installing_cURL.html) tool needs to be installed on the particular instance of the component.
- The API request you want to test in cURL format.

  For example, here's a `curl` command that you could use to make a request to an API proxy from your local machine:

  ```bsh
  curl https://myorg-test.mycompany.com/v1/customers -H 'Authorization: Bearer AxLqyU09GA10lrAiVRQCGXzMi9W2'
  ```

  Note: You can identify issues such as missing deployments with even a partial curl. As long as traffic is sent to the correct proxy for which only the correct host and basepath are necessary, you can run useful tests without having headers that would be necessary to get a success response, such as an Authorization header. In this case, the above example without the header would be a valid test case: 

  ```bsh
  curl https://myorg-test.mycompany.com/v1/customers
  ```

## How to run API requests directly against Apigee routers

### Scenario 1: API requests to host alias pointing to routers

If the DNS entry of the host alias is configured to point to the Apigee Edge routers (in other words, there is no Elastic Load Balancer (ELB)), then you could use the following curl commands to make the API requests directly to a router:

- Virtual host configured for non-secure communication via port 80

  ```bsh
  curl -v --resolve HOST_ALIAS:80:127.0.0.1 http://HOST_ALIAS/PROXY_BASE_PATH/ -H 'HEADER: VALUE'
  ```

  For example:

  ```bsh
  curl -v --resolve myorg-test.mycompany.com:80:127.0.0.1 http://myorg-test.mycompany.com/v1/customers -H 'Authorization: Bearer AxLqyU09GA10lrAiVRQCGXzMi9W2'
  ```

- Virtual host is configured to terminate SSL on port 443 on the router

  ```bsh
  curl -v --resolve HOST_ALIAS:443:127.0.0.1 https://HOST_ALIAS/PROXY_BASE_PATH/ -H 'HEADER: VALUE'
  ```

  For example:

  ```bsh
  curl -v --resolve myorg-test.mycompany.com:443:127.0.0.1 https://myorg-test.mycompany.com/v1/customers -H 'Authorization: Bearer AxLqyU09GA10lrAiVRQCGXzMi9W2'
  ```

### Scenario 2: API requests to host alias pointing to ELBs

If the DNS entry of the host alias is configured to point to an Elastic Load Balancer (ELB), then you could use the following curl commands to make the API requests directly to a router:

- Virtual host configured for non-secure communication via port 80

  ```bsh
  curl -v --resolve HOST_ALIAS:80:127.0.0.1 http://HOST_ALIAS/PROXY_BASE_PATH/ -H 'HEADER: VALUE'
  ```

  For example:

  ```bsh
  curl -v --resolve myorg-test.mycompany.com:80:127.0.0.1 http://myorg-test.mycompany.com/v1/customers -H 'Authorization: Bearer AxLqyU09GA10lrAiVRQCGXzMi9W2'
  ```

- Virtual host configured for a high port and SSL terminates on a load balancer in front of the Apigee router

  ```bsh
  curl -v --resolve HOST_ALIAS:PORT_NUMBER:127.0.0.1 http:/HOST_ALIAS:PORT_NUMBER/PROXY_BASE_PATH/ -H 'HEADER: VALUE'
  ```

  For example:

  ```bsh
  curl -v --resolve myorg-test.mycompany.com:19001:127.0.0.1 http://myorg-test.mycompany.com/v1/customers -H 'authorization: Bearer AxLqyU09GA10lrAiVRQCGXzMi9W2'
  ```

- Virtual host configured for a high port and SSL terminates on the Apigee router

  In other words, the load balancer is configured to use TCP pass-through to the Apigee router.

  ```bsh
  curl -v --resolve HOST_ALIAS:PORT_NUMBER:127.0.0.1 https:/HOST_ALIAS:PORT_NUMBER/PROXY_BASE_PATH/ -H 'HEADER: VALUE'
  ```

  For example:

  ```bsh
  curl -v --resolve myorg-test.mycompany.com:19001:127.0.0.1 https://myorg-test.mycompany.com/v1/customers -H 'authorization: Bearer AxLqyU09GA10lrAiVRQCGXzMi9W2'
  ```

## How to run requests directly against Apigee message processors

### Scenario 1: API request to message processor via default port 8998

The default port on which the message processor listens for traffic from the Apigee router is 8998. Therefore, for all cases where this port was not changed, traffic needs to be sent directly to this port on a specific message processor instance, as in the following example. The curl request has to be sent to URL `http://INTERNAL_IP_OF_MP:8998` along with the header `X-Apigee.Host` with the value hostname including the port used in the virtual hosts as shown in the following three examples:

- Virtual host is configured for SSL termination on the router

  ```bsh
  curl -v http://INTERNAL_IP_OF_MP:8998/PROXY_BASE_PATH/ -H 'HEADER: VALUE' -H 'X-Apigee.Host: HOST_ALIAS:443'
  ```

  For example:

  ```bsh
  curl -v http://10.10.53.115:8998/v1/customers -H 'Authorization: Bearer AxLqyU09GA10lrAiVRQCGXzMi9W2' -H 'X-Apigee.Host: myorg-test.mycompany.com:443'
  ```

- Virtual host is configured for a "high port" and SSL termination happens on a load balancer OR on an Apigee router:

  ```bsh
  curl -v http://INTERNAL_IP_OF_MP:8998/PROXY_BASE_PATH/ -H 'HEADER: VALUE' -H 'X-Apigee.Host: HOST_ALIAS:PORT_NUMBER'
  ```

  For example:

  ```bsh
  curl -v http://10.10.53.115:8998/v1/customers -H 'Authorization: Bearer AxLqyU09GA10lrAiVRQCGXzMi9W2' -H 'X-Apigee.Host: myorg-test.mycompany.com:19001'
  ```

- Virtual host is configured to default http port 80

  ```bsh
  curl -v http://INTERNAL_IP_OF_MP:8998/PROXY_BASE_PATH/ -H 'HEADER: VALUE' -H 'X-Apigee.Host: HOST_ALIAS:80'
  ```

  For example:

  ```bsh
  curl -v http://10.10.53.115:8998/v1/customers -H 'Authorization: Bearer AxLqyU09GA10lrAiVRQCGXzMi9W2' -H 'X-Apigee.Host: myorg-test.mycompany.com:80'
  ```

### Scenario 2: API request to message processor via SSL port 8443

It is possible to configure SSL communication between router and message processor. The following examples use port 8443, the port suggested by Apigee [documentation](https://docs.apigee.com/private-cloud/v4.18.01/configuring-ssl-between-router-and-message-processor).

- Virtual host is configured for SSL termination on the router

  ```bsh
  curl -v -k https://INTERNAL_IP_OF_MP:8443/PROXY_BASE_PATH/ -H 'HEADER: VALUE' -H 'X-Apigee.Host: HOST_ALIAS:443'
  ```

  For example:

  ```bsh
  curl -v https://10.10.53.115:8443/v1/customers -H 'Authorization: Bearer AxLqyU09GA10lrAiVRQCGXzMi9W2' -H 'X-Apigee.Host: myorg-test.mycompany.com:80'
  ```

- Virtual host is configured for SSL termination on a load balancer, and traffic is forwarded onto a high port to the router

  ```bsh
  curl -v https://INTERNAL_IP_OF_MP:8443/PROXY_BASE_PATH/ -H 'HEADER: VALUE' -H 'X-Apigee.Host: HOST_ALIAS:PORT_NUMBER'
  ```

  For example:

  ```bsh
  curl -v https://10.10.53.115:8443/v1/customers -H 'Authorization: Bearer AxLqyU09GA10lrAiVRQCGXzMi9W2' -H 'X-Apigee.Host: myorg-test.mycompany.com:19001'
  ```

  Note: The same curl command is used for requests that use mutual SSL and thus would not be testable without a client certificate.

Note: If your actual request has multiple headers you can pass them while making the API requests directly to router or message processors as well in a similar way you would do outside router/message processor.

{% endblock %}
