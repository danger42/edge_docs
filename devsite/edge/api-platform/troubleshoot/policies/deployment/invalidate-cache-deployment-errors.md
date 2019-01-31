{% extends "_base.html" %} {% block title %}Invalidate Cache policy deployment error troubleshooting{% endblock %} {% block body %}

## InvalidCacheResourceReference

### Error Message

Deployment of the API proxy through either the Edge UI or Edge management API fails with this error message:

<pre>
Error Deploying Revision <var>revision_number</var> to <var>environment</var>
Invalid cache resource reference [cache_resource] in Step definition [invalidate_cache_policy_name]. Context Revision:[revision_number];APIProxy:[apiproxy_name];Organization:[organization];Environment:[environment]
</pre>

### Example Error Message

```
Error Deploying Revision 2 to test
Invalid cache resource reference tokencache in Step definition InvalidateCache-Token. Context Revision:2;APIProxy:TestCache;Organization:kkalckstein-eval;Environment:test
```

### Example Error Screenshot

<img alt="" src="/api-platform/images/invalidatecache-deploy-error-2.png" width="75%">

### Cause

This error occurs if the `<CacheResource>` element in the InvalidateCache policy is set to a name that does not exist in the environment where the API proxy is being deployed.

### Diagnosis

1.  Identify the {{invalidate_cache_policy}} name, the invalid cache used in `<CacheResource>` element of the InvalidateCache policy, and the environment where the error occurred. You can find all these items in the error message.  For example, in the following error, the InvalidateCache policy name is `InvalidateCache-Token`, the name of the invalid cache is `tokencache`, and the environment name is `test`.

  ```
  Invalid cache resource reference tokencache in Step definition InvalidateCache-Token. Context Revision:2;APIProxy:TestCache;Organization:kkalckstein-eval;Environment:test
  ```

1.  Examine the failed InvalidateCache policy XML and verify if the cache name specified for the `<CacheResource>` element matches with the error message.

  In this example, the cache name specified in the `<CacheResource>` element is `tokencache`:

  ```
  <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
  <InvalidateCache async="false" continueOnError="false" enabled="true" name="InvalidateCache-Token">
      <DisplayName>InvalidateCache-Token</DisplayName>
      <Properties/>
      <CacheKey>
          <Prefix/>
          <KeyFragment ref=""/>
      </CacheKey>
      <CacheResource>tokencache</CacheResource>
      <Scope>Exclusive</Scope>
      <CacheContext>
          <APIProxyName/>
          <ProxyName>default</ProxyName>
          <TargetName>default</TargetName>
      </CacheContext>
      <PurgeChildEntries>false</PurgeChildEntries>
  </InvalidateCache>
  ```
  
1.  Verify if the cache (determined in step #1) has been defined in the specific environment (identified in step #1).

  In the Edge UI, navigate to **ADMIN > Environment > test** and check if the cache exists in the **Caches** tab of **Environment Configuration**.  If the cache does not exist, then that's the cause of the error.

  For example, notice in the screenshot below that the cache named `tokencache` does not exist.

  <img alt="" src="/api-platform/images/invalidatecache-deploy-error-1.png" width="100%">

  Because the cache named `tokencache` is not defined in the `test` environment, you get the error:

  ```
  Invalid cache resource reference tokencache in Step definition InvalidateCache-Token. Context Revision:2;APIProxy:TestCache;Organization:kkalckstein-eval;Environment:test
  ```

### Resolution

Ensure that the cache specified in the `<CacheResource>` element of InvalidateCache policy has been created in the environment where you want to deploy the API proxy. 

Refer to [Creating and editing an environment cache](https://docs.apigee.com/api-platform/cache/manage-caches-environment) for information on how to create the cache.

## CacheNotFound

### Error Message

Deployment of the API proxy through either the Edge UI or Edge management API results in an error message like this, and the deployment status of the API Proxy is marked as partially deployed:

<pre>
Error: Cache : <var>cache_resource</var>, not found in organization : <var>organization</var>__<var>environment</var>.</pre>

### Example Error Message

```
Error Cache : configCache, not found in organization : kkalckstein-eval__test
```

### Cause

This error occurs if the specific cache mentioned in the error message has not been created on a specific Message Processor component. A Message Processor is an internal Edge component that processes the flow of API traffic through Apigee Edge.

### Resolution

If you are a **Private Cloud** user, please follow the instructions below:

1.  Execute the [deployments management API](https://apidocs.apigee.com/docs/management/apis/get/organizations/%7Borg_name%7D/environments/%7Benv_name%7D/apis/%7Bapi_name%7D/management/apis/get/organizations/%7Borg_name%7D/environments/%7Benv_name%7D/apis/%7Bapi_name%7D/deployments)  and determine which Message Processors have the error `steps.cache.CacheNotFound`.

  <pre class="devsite-terminal">
  curl -u $USERID:$USERPASSWORD http://<management-server-host>:8080/v1/organizations/<org-name>/environments/<env-name>/apis/<apiproxy-name>/revisions/<revision-number>/deployments</pre>

  **Sample Output**

  ```
    {
      "aPIProxy" : "TestCache",
      "environment" : [ {
        "configuration" : {
          "basePath" : "/",
          "configVersion" : "SHA-512:45d3f39783414d3859bf2dec4135d8f5f9960ee6b2d361db2799c82693a8e3f8b95dbbb37c547eb3c0a3819d8ca51727f390502bcaefdf1f113263521a9023b6",
          "steps" : [ ]
        },
        "name" : "prod",
        "server" : [ {
          "pod" : {
            "name" : "pod1",
            "region" : "us-central1"
          },
          "status" : "deployed",
          "type" : [ "message-processor" ],
          "uUID" : "f2e5e34a-5630-43a9-8fef-48a5b9da76d1"
        }, {
          "pod" : {
            "name" : "pod1",
            "region" : "us-central1"
          },
          "status" : "deployed",
          "type" : [ "message-processor" ],
          "uUID" : "879a6538-a5e0-4503-b142-9cb2b4e0623d"
        }, { 
        "error" : "Cache : configCache, not found in organization : kkalckstein-eval__test", 
        "errorCode" : "steps.cache.CacheNotFound", 
        "status" : "error", 
        "type" : [ "message-processor" ], 
         "uUID" : "a8f9ce0b-c32d-48a9-b26c-9c75d8bf467d" 
    }, 
    ...
        "state" : "deployed"
      } ],
      "name" : "2",
      "organization" : "kkalckstein-eval"
      ...
  ```


1.  Note the UUID(s) of the Message Processor in which you observe the error `steps.cache.CacheNotFound`. Identify the host name/IP address of the Message Processor corresponding to the UUID.
1.  Log in to the specific Message Processor and restart it using the following command:

	`apigee-service edge-message-processor restart`

If you are a **Public Cloud** user or if the issue persists for **Private Cloud**, contact [Apigee Support](https://apigee.com/about/support/portal) for assistance.

{% endblock %}
