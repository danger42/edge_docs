{% extends "_base.html" %} {% block title %}Quota runtime error troubleshooting{% endblock %} {% block body %}

<aside class="note">Was this troubleshooting playbook helpful? Please let us know
  by clicking <a href="" class="google-feedback"
   data-p="1636213" data-b="docsite" data-context="troubleshooting-feedback">
  Send Feedback 
</a>.</aside>

## InvalidMessageWeight

### Error code

```json
policies.ratelimit.InvalidMessageWeight
```

### Error response body

```json
{
    "fault": {
        "faultstring": "Invalid message weight value [invalid_value]",
        "detail": {
            "errorcode": "policies.ratelimit.InvalidMessageWeight"
        }
    }
}
```

**Example Error Message**

```json
{
    "fault": {
        "faultstring": "Invalid message weight value 1.5",
        "detail": {
            "errorcode": "policies.ratelimit.InvalidMessageWeight"
        }
    }
}
```

### Cause

This error occurs if the value of the `<MessageWeight>` element specified through a flow variable is invalid (a non-integer value). 

For example, this error will occur if the value of the flow variable specified for the `<MessageWeight>` element is 1.5 (a non-integer value).


### Diagnosis

1.  Identify the invalid value used for the `<MessageWeight>` element in the Quota policy. You can find this information in the `faultstring` element of the error response. For example, in the following error, the invalid value used for  `<MessageWeight>` element is `1.5`:

    ```json
    "faultstring": "Invalid message weight value 1.5"
    ```

1.  Examine all the Quota policies in the specific API Proxy where the failure has occurred. There could be one or more Quota policies in which the `<MessageWeight>` element is specified.

   For example, the following policy specifies the `<MessageWeight> `through a flow variable` message_weight`:

  ```xml
  <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
      <Quota async="false" continueOnError="false" enabled="true" name="Quota_with_weight" type="calendar">
      <DisplayName>Quota_with_weight</DisplayName>
      <Properties/>
      <Allow count="3"/>
      <Interval>1</Interval>
      <TimeUnit>minute</TimeUnit>
      <StartTime>2017-7-16 12:00:00</StartTime>
      <MessageWeight ref="message_weight"/>
  </Quota>
  ```

1.  Determine the value of the variable used for `<MessageWeight>`  in the identified Quota Policy(ies). The value of the flow variable can be extracted from HTTP headers, query parameters, an XML or JSON request payload, or defined in another policy:
    1.  Locate the code within the API Proxy bundle where the variable was defined first.
    1.  Once you figure out the policy in which the variable is defined and populated first, then check how the value for the variable is set.
    1.  If the value of the flow variable matches the value identified in Step #1 above, then that's the cause of the error.

    For example, assume that a JavaScript policy used before the Quota Policy sets the variable `message_weight` based on the request type as shown below:

  ```javascript
  var verb = context.getVariable("request.verb");
  context.setVariable("message_weight", "1.5");
  if (verb == 'POST') {
    context.setVariable("message_weight", "2");    
  }
  ```

  Notice that the value of the variable `message_weight` is `1.5` which is invalid (a non-integer) value.

### Resolution

Ensure that the value representing the `MessageWeight` specified by the flow variable is a valid value (integer value).

To correct the example shown above, you can modify the value of the variable `message_weight` in the JavaScript to be an integer. 


```javascript
var verb = context.getVariable("request.verb");
context.setVariable("message_weight", "1");
if (verb == 'POST') {
  context.setVariable("message_weight", "2");    
}
```


## FailedToResolveQuotaIntervalReference


### Error code

```json
policies.ratelimit.FailedToResolveQuotaIntervalReference
```

### Error response body

```json
{
    "fault": {
        "faultstring": "Failed to resolve quota interval reference [reference] in quota policy {1}",
        "detail": {
            "errorcode": "policies.ratelimit.FailedToResolveQuotaIntervalReference"
        }
    }
}
```

**Example Error Message**

```json
{
    "fault": {
        "faultstring": "Failed to resolve quota interval reference api.product.developer.quota.interval in quota policy {1}",
        "detail": {
            "errorcode": "policies.ratelimit.FailedToResolveQuotaIntervalReference"
        }
    }
}
```

### Cause

This error occurs if the `<Interval> `element is not defined within the Quota policy.  This element is mandatory and used to specify the interval of time applicable to the quota. The time interval can be minutes, hours, days, weeks, or months as defined with the `<TimeUnit>` element.

### Diagnosis

1.  Examine each of the Quota policies in the API Proxy where the failure occurred. If there is any Quota policy where the mandatory element `<Interval>` is not defined, then that's the cause of the error.

    For example, the following Quota policy does not have the mandatory element `<Interval>`:

  ```xml
  <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
      <Quota async="false" continueOnError="false" enabled="true" name="CheckQuota" type="calendar">
      <DisplayName>CheckQuota</DisplayName>
      <Properties/>
      <Allow count="3"/>
          <TimeUnit ref="verifyapikey.verify-api-key.apiproduct.developer.quota.timeunit">hour</TimeUnit>
      <StartTime>2017-7-16 12:00:00</StartTime>
  </Quota>
  ```

  Because the mandatory element  `<TimeUnit>` is not defined in the above Quota policy, you will receive the error code:

  ```json
  policies.ratelimit.FailedToResolveQuotaIntervalReference
  ```

### Resolution

Ensure that all the Quota policies for a given API proxy have the mandatory element `<Interval>` defined properly. 

To correct the example shown above, you can modify the policy to include the element `<Interval>` as shown below.` `

<pre class="prettyprint">
&lt;?xml version=&quot;1.0&quot; encoding=&quot;UTF-8&quot; standalone=&quot;yes&quot;?&gt;
&lt;Quota async=&quot;false&quot; continueOnError=&quot;false&quot; enabled=&quot;true&quot; name=&quot;CheckQuota&quot; type=&quot;calendar&quot;&gt;
    &lt;DisplayName&gt;CheckQuota&lt;/DisplayName&gt;
    &lt;Properties/&gt;
    &lt;Allow count=&quot;3&quot;/&gt;
    &lt;TimeUnit ref=&quot;verifyapikey.verify-api-key.apiproduct.developer.quota.timeunit&quot;&gt;hour&lt;/TimeUnit&gt;
    <b>&lt;Interval ref=&quot;verifyapikey.verify-api-key.apiproduct.developer.quota.interval&quot;&gt;1&lt;/Interval&gt;</b>
    &lt;StartTime&gt;2017-7-16 12:00:00&lt;/StartTime&gt;
&lt;/Quota&gt;
</pre>

## FailedToResolveQuotaIntervalTimeUnitReference


### Error code

```json
policies.ratelimit.FailedToResolveQuotaIntervalTimeUnitReference
```

### Error response body

```json
{
    "fault": {
        "faultstring": "Failed to resolve quota time unit reference [reference] in quota policy {1}",
        "detail": {
            "errorcode": "policies.ratelimit.FailedToResolveQuotaIntervalTimeUnitReference"
        }
    }
}
```

**Example Error Message**

```json
{
    "fault": {
        "faultstring": "Failed to resolve quota time unit reference apiproduct.developer.quota.timeunity in quota policy {1}",
        "detail": {
            "errorcode": "policies.ratelimit.FailedToResolveQuotaIntervalTimeUnitReference"
        }
    }
}
```

### Cause

This error occurs if the  `<TimeUnit> `element is not defined within the Quota policy.  This element is mandatory and used to specify the unit of time applicable to the quota. The time interval can be in minutes, hours, days, weeks, or months.

### Diagnosis

1.  Examine each of the Quota policies in the API Proxy where the failure occurred. If there is any Quota policy where the mandatory element `<TimeUnit>` is not defined, then that's the cause of the error.

  For example, the following Quota policy does not have the mandatory element `<TimeUnit>`:

  ```xml
  <Quota async="false" continueOnError="false" enabled="true" name="CheckQuota" type="calendar">
    <DisplayName>CheckQuota</DisplayName>
    <Properties/>
    <Allow count="3"/>
        <Interval ref="verifyapikey.verify-api-key.apiproduct.developer.quota.interval">1</Interval>
    <StartTime>2017-7-16 12:00:00</StartTime>
  </Quota>
  ```

   Because the mandatory element  `<TimeUnit>` is not defined in the above Quota policy, you will receive the error code:

  ```json
  policies.ratelimit.FailedToResolveQuotaIntervalTimeUnitReference
  ```

### Resolution

Ensure that all the Quota policies for a given API proxy have the mandatory element `<TimeUnit>` defined. 

To correct the example shown above, you can modify the policy to include the element `<TimeUnit>` as shown below.` `


<pre class="prettyprint">
&lt;Quota async=&quot;false&quot; continueOnError=&quot;false&quot; enabled=&quot;true&quot; name=&quot;CheckQuota&quot; type=&quot;calendar&quot;&gt;
    &lt;DisplayName&gt;CheckQuota&lt;/DisplayName&gt;
    &lt;Properties/&gt;
    &lt;Allow count=&quot;3&quot;/&gt;
    <b>&lt;TimeUnit ref=&quot;verifyapikey.verify-api-key.apiproduct.developer.quota.timeunit&quot;&gt;hour&lt;/TimeUnit&gt;</b>
    &lt;Interval ref=&quot;verifyapikey.verify-api-key.apiproduct.developer.quota.interval&quot;&gt;1&lt;/Interval&gt;
    &lt;StartTime&gt;2017-7-16 12:00:00&lt;/StartTime&gt;
&lt;/Quota&gt;
</pre>

{% endblock %}
