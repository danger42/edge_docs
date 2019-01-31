{% extends "_base.html" %} {% block title %}Lookup Cache policy deployment error troubleshooting{% endblock %} {% block body %}

## InvalidTimeout

### Error Message

Deployment of the API proxy through either the Edge UI or Edge management API fails with this error message:

<pre>
Error Saving Revision <var>revision_number</var>
CacheLookupTimeoutInSeconds <var>value</var> value should be greater than zero.
</pre>

#### Example Error Message

```
Error Saving Revision 2
CacheLookupTimeoutInSeconds -1 value should be greater than zero.
```

#### Example Error Screenshot

<img alt="" src="/api-platform/images/lookupcache-error-1.png" width="75%">

### Cause

If the `<CacheLookupTimeoutInSeconds>` element of a {{lookup_cache_policy}}  is set to a negative number, then the deployment of the API proxy fails.

For example, if the `<CacheLookupTimeoutInSeconds>` element is `-1`, then the deployment of the API proxy fails.

### Diagnosis

1.  Identify the invalid value specified for the `<CacheLookupTimeoutInSeconds>` element in the {{lookup_cache_policy}}. You can find this information in the error message. For example, in the following error, the invalid value used for `<CacheLookupTimeoutInSeconds>` element  is `-1`:

  ```
  CacheLookupTimeoutInSeconds -1 value should be greater than zero.
  ```

1.  Examine all the LookupCache policies in the specific API Proxy where the failure has occurred. There could be one or more LookupCache policies in which the `<CacheLookupTimeoutInSeconds>` element is specified. Identify the LookUpCache policy where the invalid value (identified in step #1 above) is specified for `<CacheLookupTimeoutInSeconds>` element.

  For example, the following policy configuration specifies the value of `<CacheLookupTimeoutInSeconds>` of `-1`, which matches the error message:

  ```
  <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
  <LookupCache async="false" continueOnError="false" enabled="true" name="LookupCache-Token">
      <DisplayName>LookupCache-Token</DisplayName>
      <Properties/>
      <CacheKey>
          <Prefix/>
          <KeyFragment ref="request.queryparam.client_id"/>
      </CacheKey>
      <CacheLookupTimeoutInSeconds>-1</CacheLookupTimeoutInSeconds>
      <Scope>Exclusive</Scope>
      <ExpirySettings>
          <TimeoutInSec>3600</TimeoutInSec>
      </ExpirySettings>
      <AssignTo>usertoken</AssignTo>
  </LookupCache>
  ```


1.  If the `<CacheLookupTimeoutInSeconds>` is specified as a negative integer, then that's the cause of the error.


### Resolution

Ensure that the value for the  `<CacheLookupTimeoutInSeconds>` element of the LookupCache policy is always specified as a non-negative integer.

To correct the example LookupCache policy shown above, you could modify the `<CacheLookupTimeoutInSeconds>` element to `30`.


```
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<LookupCache async="false" continueOnError="false" enabled="true" name="LookupCache-Token">
    <DisplayName>LookupCache-Token</DisplayName>
    <Properties/>
    <CacheKey>
        <Prefix/>
        <KeyFragment ref="request.queryparam.client_id"/>
    </CacheKey>
    <CacheResource>tokencache</CacheResource>
    <CacheLookupTimeoutInSeconds>30</CacheLookupTimeoutInSeconds>
    <Scope>Exclusive</Scope>
    <ExpirySettings>
        <TimeoutInSec>3600</TimeoutInSec>
    </ExpirySettings>
    <AssignTo>usertoken</AssignTo>
</LookupCache>
```


## InvalidCacheResourceReference


### Error Message

Deployment of the API proxy through either the Edge UI or Edge management API fails with this error message:

<pre>
Error Deploying Revision <var>revision_number</var> to <var>environment</var>
Invalid cache resource reference [cache_resource] in Step definition [populate_cache_policy_name]. Context Revision:[revision_number];APIProxy:ResponseCache;Organization:[organization];Environment:[environment]
</pre>

#### Example Error Message

```
Error Deploying Revision 2 to test
Invalid cache resource reference tokencache in Step definition LookupCache-Token. Context Revision:2;APIProxy:TestCache;Organization:kkalckstein-eval;Environment:test
```

#### Example Error Screenshot

<p><img alt="" src="/api-platform/images/lookupcache-error-2.png" width="75%"></p>

### Cause

This error occurs if the `<CacheResource>` element is set to a name which does not exist in the environment where the API proxy is being deployed.

### Diagnosis


1.  Identify the invalid cache used in `<CacheResource>` element of the {{lookup_cache_policy}} and the environment where the error occurred. You can find both of these items in the error message.  For example, in the following error, the name of the invalid cache is `tokencache` and the environment name is `test`.

  ```
  Invalid cache resource reference tokencache in Step definition LookupCache-Token. Context Revision:2;APIProxy:TestCache;Organization:kkalckstein-eval;Environment:test
  ```


1.  Examine all the LookupCache policies in the specific API Proxy where the failure has occurred. Identify the specific LookupCache policy where the invalid cache (identified in step #1) is specified in `<CacheResource>` element is specified.

  For example, the following policy specifies the value of `<CacheResource>` as `tokencache`, which matches the error message:


  ```
  <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
  <LookupCache async="false" continueOnError="false" enabled="true" name="LookupCache-Token">
      <DisplayName>LookupCache-Token</DisplayName>
      <Properties/>
      <CacheKey>
          <Prefix/>
          <KeyFragment ref="request.queryparam.client_id"/>
      </CacheKey>
      <CacheResource>tokencache</CacheResource>
      <CacheLookupTimeoutInSeconds/>
      <Scope>Exclusive</Scope>
      <ExpirySettings>
          <TimeoutInSec>3600</TimeoutInSec>
      </ExpirySettings>
      <AssignTo>usertoken</AssignTo>
  </LookupCache>
  ```


1.  Verify if the cache (determined in step #1) has been defined in the specific environment (identified in step #1).

  In the Edge UI, navigate to **ADMIN > Environment > test** and check if the cache exists in the **Caches** tab of **Environment Configuration**.  If the cache does not exist, then that's the cause of the error.


  For example, notice in the screenshot below that the cache named `tokencache` does not exist.

  <img alt="" src="/api-platform/images/lookupcache-error-3.png" width="100%">

  Because the cache named `tokencache` is not defined in the `test` environment, you get the error:


  ```
  Invalid cache resource reference tokencache in Step definition LookupCache-Token. Context Revision:2;APIProxy:TestCache;Organization:kkalckstein-eval;Environment:test

  ```

### Resolution

Ensure that the cache name specified in the `<CacheResource>` element has been created in the environment where you want to deploy the API proxy.


Refer to [Creating and editing an environment cache](https://docs.apigee.com/api-platform/cache/manage-caches-environment) for information on how to create the cache.

## CacheNotFound

### Error Message

Deployment of the API proxy through either the Edge UI or Edge management API results in an error message like this, and the deployment status of the API Proxy is marked as partially deployed:

<pre>
Error: Cache : <var>cache_resource</var>, not found in organization : <var>organization</var>__<var>environment</var>.
</pre>

### Example Error Message

<pre>
Error Cache : configCache, not found in organization : kkalckstein-eval__test
</pre>

### Cause

This error occurs if the specific cache mentioned in the error message has not been created on a specific Message Processor component. 

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

1.  Note the UUID(s) of the Message Processor in which you observe the error `steps.cache.CacheNotFound`. Identify the host name/IP address of the Message Processor associated with the UUID.
1.  Log in to the specific Message Processor and restart it using the following command:

	`apigee-service edge-message-processor restart`

If you are a **Public Cloud** user or if the issue persists for **Private Cloud**, contact [Apigee Support](https://apigee.com/about/support/portal) for assistance.

{% endblock %}
