{% extends "_base.html" %} {% block title %}Response Cache policy deployment error troubleshooting{% endblock %} {% block body %}

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

<img alt="" src="/api-platform/images/responsecache-error-3.png" width="75%">

### Cause

If the `<CacheLookupTimeoutInSeconds>` element of a {{response_cache_policy}} is set to a negative number, then the deployment of the API proxy fails.

For example, if the `<CacheLookupTimeoutInSeconds>` is `-1`, then the deployment of the API proxy fails.

### Diagnosis

1.  Identify the invalid value used for the `<CacheLookupTimeoutInSeconds>` element in the ResponseCache policy. You can find this information in the error message. For example, in the following error, the invalid value used for `<CacheLookupTimeoutInSeconds>` element  is `-1`:

  ```
  CacheLookupTimeoutInSeconds -1 value should be greater than zero.
  ```

1.  Examine all the ResponseCache policies in the specific API Proxy where the failure has occurred. There could be one or more ResponseCache policies in which the `<CacheLookupTimeoutInSeconds>` element is specified.

  For example, the following policy configuration sets `<CacheLookupTimeoutInSeconds>` to `-1`, which matches with what's in the error message:

  ```
  <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
  <ResponseCache async="false" continueOnError="false" enabled="true" name="Response-Cache-1">
      <DisplayName>Response Cache-1</DisplayName>
      <Properties/>
      <CacheKey>
          <Prefix/>
          <KeyFragment ref="request.uri" type="string"/>
      </CacheKey>
      <Scope>Exclusive</Scope>
      <ExpirySettings>
          <ExpiryDate/>
          <TimeOfDay/>
          <TimeoutInSec ref="">3600</TimeoutInSec>
      </ExpirySettings>
      <CacheLookupTimeoutInSeconds>-1</CacheLookupTimeoutInSeconds>
  </ResponseCache>
  ```

1.  If the `<CacheLookupTimeoutInSeconds>` is specified as a negative integer, then that's the cause of the error.

### Resolution

Ensure that the value for the  `<CacheLookupTimeoutInSeconds>` element of the ResponseCache policy is always specified as a non-negative integer.

To correct the example ResponseCache policy shown above, you could modify the `<CacheLookupTimeoutInSeconds> element` to `30`.

```
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<ResponseCache async="false" continueOnError="false" enabled="true" name="Response-Cache-1">
    <DisplayName>Response Cache-1</DisplayName>
    <Properties/>
    <CacheKey>
        <Prefix/>
        <KeyFragment ref="request.uri" type="string"/>
    </CacheKey>
    <Scope>Exclusive</Scope>
    <ExpirySettings>
        <ExpiryDate/>
        <TimeOfDay/>
        <TimeoutInSec ref="">3600</TimeoutInSec>
    </ExpirySettings>
    <CacheLookupTimeoutInSeconds>30</CacheLookupTimeoutInSeconds>
</ResponseCache>
```

## InvalidCacheResourceReference

### Error Message

Deployment of the API proxy through either the Edge UI or Edge management API fails with this error message:

<pre>
Error Deploying Revision <var>revision_number</var> to <var>environment</var>
Invalid cache resource reference <var>cache_resource</var> in Step definition <var>response_cache_policy_name</var>. Context Revision:<var>revision_number</var>;APIProxy:ResponseCache;Organization:<var>organization</var>;Environment:<var>environment</var>
</pre>

#### Example Error Message

```
Error Deploying Revision 2 to prod
Invalid cache resource reference itemscache in Step definition ItemsResponseCache. Context Revision:2;APIProxy:StoresInventory;Organization:kkalckstein-eval;Environment:prod
```

#### Example Error Screenshot

<img alt="" src="/api-platform/images/responsecache-error-4.png" width="75%">

### Cause

This error occurs if the `<CacheResource>` element in a {{response_cache_policy}} is set to a name that does not exist in the environment where the API proxy is being deployed.

### Diagnosis

1.  Identify the invalid cache used in `<CacheResource>` element of the Response Cache policy and the environment where the error occurred. You can find both of these items in the error message.  For example, in the following error, the name of the invalid cache is `itemscache` and the environment name is `prod`.

  ```
  Invalid cache resource reference itemscache in Step definition ItemsResponseCache. Context Revision:2;APIProxy:StoresInventory;Organization:kkalckstein-eval;Environment:prod
  ```

1. Examine all the ResponseCache policies in the specific API Proxy where the failure
  has occurred. Identify the specific ResponseCache policy where the invalid cache
  (identified in step #1) is specified in `<CacheResource>` element.

  For example, the following policy specifies the value of `<CacheResource>` as
  `itemscache`, which matches with what’s in the error message:

  ```
  <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
  <ResponseCache async="false" continueOnError="false" enabled="true" name="ItemsResponseCache">
    <DisplayName>ItemsResponseCache</DisplayName>
    <Properties/>
    <CacheKey>
        <Prefix/>
        <KeyFragment ref="request.uri" type="string"/>
    </CacheKey>
    <CacheResource>itemscache</CacheResource>
    <Scope>Exclusive</Scope>
    <ExpirySettings>
        <ExpiryDate/>
        <TimeOfDay/>
        <TimeoutInSec ref="">3600</TimeoutInSec>
    </ExpirySettings>
    <SkipCacheLookup/>
    <SkipCachePopulation/>
  </ResponseCache>
  ```

1.   Verify if the cache (determined in step #2) has been defined in the specific environment (identified in step #1).

  In the Edge UI, navigate to **APIs > Environment Configuration** and check if the cache exists in the **Caches** tab in the specific environment.  If the cache does not exist, then that's the cause of the error.

  For example, notice in the screenshot below that the cache named `itemscache` does not exist.

  <img alt="" src="/api-platform/images/responsecache-error-7.png" width="75%" class="screenshot">

  Because the cache named `itemscache` is not defined in the `prod` environment, you get the error:

  ```
  Invalid cache resource reference does_not_exist in Step definition Response-Cache-1. Context Revision:2;APIProxy:ResponseCache;Organization:kkalckstein-eval;Environment:prod
  ```

### Resolution

Ensure that the cache name specified in the `<CacheResource>` element has been created in the environment where you want to deploy the API proxy. 

Refer to [Creating and editing an environment cache](https://docs.apigee.com/api-platform/cache/manage-caches-environment) for information on how to create the cache.


## ResponseCacheStepAttachmentNotAllowedReq

### Error Message

Deployment of the API proxy through either the Edge UI or Edge management API fails with this error message:

<pre>
Error Deploying Revision <var>revision_number</var> to <var>environment</var>
Response cache step definition <var>response_cache_policy_name</var> can not be attached more than once in the request path.
</pre>

#### Example Error Message

```
Error Deploying Revision 2 to test
Response cache step definition Response-Cache-1 can not be attached more than once in the request path.
```

#### Example Error Screenshot

<img alt="" src="/api-platform/images/responsecache-error-6.png" width="75%">

### Cause

This error occurs if the same {{response_cache_policy}} is attached to multiple request paths within any [flows](/api-platform/fundamentals/what-are-flows) of an API proxy.

For example, if you have the same ResponseCache policy attached in the request Preflow of both the Proxy and Target Endpoints, then this error occurs.

### Diagnosis

1.  Identify the name of the ResponseCache policy that is attached more than once. You can find this information in the error message.  For example, in the following error the name of the ResponseCache policy is Response&#8209;Cache&#8209;1.

  ```
  Error Deploying Revision 2 to test
  Response cache step definition Response-Cache-1 can not be attached more than once in the request path.
  ```


1.  Examine all the request flows in the Proxy and Target Endpoints of the API Proxy where the error occurred. If the same ResponseCache policy is attached in two or more request flows, then that's the cause of the error.

  In the following example the same ResponseCache policy `Response-Cache-1` is configured in the request path of the default proxy endpoint PreFlow, and the default target endpoint PreFlow:

  ```
  <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
  <ProxyEndpoint name="default">
      <Description/>
      <FaultRules/>
      <PreFlow name="PreFlow">
          <Request>
              <Step>
                  <Name>Response-Cache-1</Name>
              </Step>
          </Request>
      ...
  <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
  <TargetEndpoint name="default">
      <Description/>
      <FaultRules/>
      <PreFlow name="PreFlow">
          <Request/>
          <Response/>
      </PreFlow>
      <PostFlow name="PostFlow">
          <Request>
              <Step>
                  <Name>Response-Cache-1</Name>
              </Step>
          </Request>
      ...
  ```

  Tip: In the UI, click a flow under **Proxy Endpoint** in the Navigator and a flow
  under **Target Endpoint**.

  
### Resolution

Ensure a ResponseCache policy is attached to only one request path across all flows of the API Proxy.

To correct the example shown above, remove the ResponseCache policy `Response-Cache-1` from one of the two request flows.

## ResponseCacheStepAttachmentNotAllowedResp

### Error Message

Deployment of the API proxy through either the Edge UI or Edge management API fails with this error message:

<pre>
Error Deploying Revision <var>revision_number</var> to <var>environment</var>
Response cache step definition <var>response_cache_policy_name</var> can not be attached more than once in the response path.
</pre>

#### Example Error Message

```
Error Deploying Revision 2 to test
Response cache step definition Response-Cache-1 can not be attached more than once in the response path.
```

#### Example Error Screenshot

<img alt="" src="/api-platform/images/responsecache-error-5.png" width="75%">

### Cause

This error occurs if the same {{response_cache_policy}} is attached to multiple response paths within any flows of an API proxy.

For example, if you have the same ResponseCache policy attached in the response Preflow of both the Proxy and Target Endpoints, then this error occurs.

### Diagnosis

1.  Identify the name of the ResponseCache policy that is attached more than once. You can find this information in the error message.  For example, in the following error the name of the ResponseCache policy is `Response-Cache-1`.

  ```
  Error Deploying Revision 2 to test
  Response cache step definition Response-Cache-1 can not be attached more than once in the response path.
  ```


1.  Examine all the request flows in the Proxy and Target Endpoints of the API Proxy where the error occurred. If the same ResponseCache policy is attached in two or more response flows, then that's the cause of the error.

  In the following example the same ResponseCache policy `Response-Cache-1` is configured in the response path of the default proxy endpoint PreFlow, and the default target endpoint PreFlow:


  ```
  <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
  <ProxyEndpoint name="default">
      <Description/>
      <FaultRules/>
      <PreFlow name="PreFlow">
          <Request>
              <Step>
                  <Name>Response-Cache</Name>
              </Step>
          </Request>
          <Response>
              <Step>
                  <Name>Response-Cache-1</Name>
              </Step>
          </Response>
      </PreFlow>
      ...
  <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
  <TargetEndpoint name="default">
      <Description/>
      <FaultRules/>
      <PreFlow name="PreFlow">
          <Request/>
          <Response/>
      </PreFlow>
      <PostFlow name="PostFlow">
          <Request/>
          <Response>
              <Step>
                  <Name>Response-Cache-1</Name>
              </Step>
          </Response>
      </PostFlow>
      ...
  ```

### Resolution

Ensure a ResponseCache policy is attached to only one response path across all flows of the API Proxy.

To correct the example shown above, remove the ResponseCache policy `Response-Cache-1` from one of the two response paths.


## InvalidMessagePatternForErrorCode

### Error Message

Deployment of the API proxy through either the Edge UI or Edge management API fails with either of these error messages:

<pre>
Error Deploying Revision <var>revision_number</var> to <var>environment</var>
Invalid message pattern found for error code steps.cache.InvalidSkipCacheLookUpCondition.
</pre>

OR

<pre>
Error Deploying Revision <var>revision_number</var> to <var>environment</var>
Invalid message pattern found for error code steps.cache.InvalidSkipCachePopulationCondition.
</pre>

#### Example Error Message

```
Error Deploying Revision 2 to prod
Invalid message pattern found for error code steps.cache.InvalidSkipCacheLookUpCondition.
```

OR

```
Error Deploying Revision 2 to prod
Invalid message pattern found for error code steps.cache.InvalidSkipCachePopulationCondition.
```

#### Example Error Screenshot

<img alt="" src="/api-platform/images/responsecache-error-2.png" width="75%">


OR

<img alt="" src="/api-platform/images/responsecache-error-1.png" width="75%">

### Cause

This error occurs if either the `<SkipCacheLookup>` or `<SkipCachePopulation>` element in a {{response_cache_policy}} contains an invalid condition.

### Diagnosis

1.  Examine all the ResponseCache policies in the API Proxy where the error occurred and check if there are any policies that have conditions specified for  `<SkipCacheLookup>` and/or the `<SkipCachePopulation>` elements.

1.  Check if the condition specified for the `<SkipCacheLookup>` and/or the `<SkipCachePopulation>` element is invalid.  If yes, then that's the cause of the error.

  In the following example, the `<SkipCachePopulation> `element uses the JavaScript operator **===** to check for equal value and equal type which is invalid.

  ```
  <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
  <ResponseCache async="false" continueOnError="false" enabled="true" name="Response-Cache-1">
      <DisplayName>Response Cache-1</DisplayName>
      <Properties/>
      <CacheKey>
          <Prefix/>
          <KeyFragment ref="request.uri" type="string"/>
      </CacheKey>
      <Scope>Exclusive</Scope>
      <ExpirySettings>
          <ExpiryDate/>
          <TimeOfDay/>
          <TimeoutInSec ref="">3600</TimeoutInSec>
      </ExpirySettings>
      <CacheLookupTimeoutInSeconds>2</CacheLookupTimeoutInSeconds>
      <SkipCacheLookup>request.header.bypass-cache === "true"</SkipCacheLookup>
  </ResponseCache>
  ```

  Because the operator `===`  is invalid, you get the error:

  ```
  Invalid message pattern found for error code steps.cache.InvalidSkipCacheLookUpCondition.
  ```

### Resolution

Ensure the condition specified for  the `<SkipCacheLookup>` and/or `<SkipCachePopulation>` elements is always valid.

To correct the example {{response_cache_policy}} shown above, you could modify the `<SkipCacheLookup>` to use the `=` operator:

```
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<ResponseCache async="false" continueOnError="false" enabled="true" name="Response-Cache-1">
    <DisplayName>Response Cache-1</DisplayName>
    <Properties/>
    <CacheKey>
        <Prefix/>
        <KeyFragment ref="request.uri" type="string"/>
    </CacheKey>
    <Scope>Exclusive</Scope>
    <ExpirySettings>
        <ExpiryDate/>
        <TimeOfDay/>
        <TimeoutInSec ref="">3600</TimeoutInSec>
    </ExpirySettings>
    <CacheLookupTimeoutInSeconds>2</CacheLookupTimeoutInSeconds>
    <SkipCacheLookup>request.header.bypass-cache = "true"</SkipCacheLookup>
</ResponseCache>
```

## CacheNotFound


### Error Message

Deployment of the API proxy through either the Edge UI or Edge management API results in an error message like this, and the deployment status of the API Proxy is marked as partially deployed:

<pre>
Error: Cache : <var>cache_resource</var>, not found in organization : <var>organization</var>__<var>environment</var>.
</pre>

#### Example Error Message

```
Error Cache : Response-Cache-1, not found in organization : kkalckstein-eval__prod
```

### Cause

This error occurs if the specific cache mentioned in the error message has not been created on a specific Message Processor component. 

### Resolution

If you are a **Private Cloud** user, follow the instructions below:

1.  Execute the [deployments management API](https://apidocs.apigee.com/docs/management/apis/get/organizations/%7Borg_name%7D/apis/%7Bapi_name%7D/revisions/%7Brevision_number%7D/deployments)  and determine which Message Processors have the error `steps.cache.CacheNotFound`.

 **Sample Output**

  ```
  curl -u $USERID:$USERPASSWORD http://<management-server-host>:8080/v1/organizations/<org-name>/environments/<env-name>/apis/<apiproxy-name>/revisions/<revision-number>/deployments
  {
    "aPIProxy" : "ResponseCache",
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
      "error" : "Cache : Response-Cache-1, not found in organization : kkalckstein-eval__prod", 
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
  ```

1.  Note the UUID(s) of the Message Processor in which you observe the error `steps.cache.CacheNotFound`. Identify the host name/IP address of the Message Processor from the UUID.
1.  Log in to the specific Message Processor and restart it using the following command:


	`apigee-service edge-message-processor restart`

If you are a **Public Cloud** user or if the issue persists for Private Cloud, contact [Apigee Support](https://apigee.com/about/support/portal) for assistance.

{% endblock %}
