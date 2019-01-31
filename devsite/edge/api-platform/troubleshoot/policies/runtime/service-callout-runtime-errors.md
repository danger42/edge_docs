{% extends "_base.html" %} {% block title %}Service Callout runtime error troubleshooting{% endblock %} {% block body %}

<aside class="note">Was this troubleshooting playbook helpful? Please let us know
  by clicking <a href="" class="google-feedback"
   data-p="1636213" data-b="docsite" data-context="troubleshooting-feedback">
  Send Feedback 
</a>.</aside>


## RequestVariableNotMessageType


### Error code

```
steps.servicecallout.RequestVariableNotMessageType
```



### Error response body


<pre class='prettyprint'>
{
    "fault": {
        "faultstring": "ServiceCallout[<b><i>policy_name</i></b>]: request variable [<b><i>variable_name</i></b>] value is not of type Message",
        "detail": {
            "errorcode": "steps.servicecallout.RequestVariableNotMessageType"
        }
    }
}
</pre>



### Cause

This error occurs if a variable specified in the `<Request>` element of the [Service Callout policy](https://docs.apigee.com/api-platform/reference/policies/service-callout-policy#request) is not of type [message](https://docs.apigee.com/api-platform/reference/variables-reference#messagevariables). If the variable is a string or any other non-message type, then you'll see this error.

Message type variables represent entire HTTP requests and responses. The built-in Edge flow variables `request`, `response`, and `message` are of type message. To learn more about message variables, see the [Variables reference](https://docs.apigee.com/api-platform/reference/variables-reference#messagevariables). 


### Diagnosis



1.  Identify the Service Callout policy where the error occurred and the name of the variable whose type is incorrect. You can find both of these items in the `faultstring` element of the error response. For example, in the following `faultstring`, the policy name is `ExecuteGeocodingRequest` and the variable is `PostalCode`:
 
    <code> "faultstring": "ServiceCallout[<strong>ExecuteGeocodingRequest</strong>]: request variable <strong>PostalCode</strong> value is not of type Message"</code>


1.  In the failed Service Callout policy XML, verify that the name of the variable set in the `<Request>` element matches the variable name identified in the fault string (step #1 above). For example, the following policy specifies a request variable named `PostalCode`, which matches what's in the `faultstring`: 


    ```
    <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
    <ServiceCallout name="ExecuteGeocodingRequest">
        <Request variable="PostalCode"/>
        <Response>GeocodingResponse</Response>
        <HTTPTargetConnection>
            <URL>http://maps.googleapis.com/maps/api/geocode/json</URL>
        </HTTPTargetConnection>
    </ServiceCallout>
    ```


1.  Determine if this variable is of type message or not:
    1.  Locate the code within the API Proxy bundle, where the variable was defined first. 
    1.  In most cases, you'll find that the problem variable is created and populated in another policy that executes before the Service Callout policy. For example, the Assign Message policy is commonly used to create and populate variables in an API proxy flow.
    1.  Once you figure out the policy in which the variable is defined and populated first, you need to determine the type of that variable as follows:
        * Check the value of the `type` attribute (if present).
        * If the `type` attribute is not present, then the variable is considered to be a string.
    1.  If the variable's type is non-message (such as a string), then that's the cause of the error. You can learn about common variables and their types in the [Variables reference](https://docs.apigee.com/api-platform/reference/variables-reference).

As an example, assume the `PostalCode` variable referenced in the Service Callout policy was created in the following Assign Message policy. Note that `PostalCode` is assigned the value of the flow variable `request.queryparam.postalcode`. This value is a string, because there is no `type` attribute present in the variable assignment.


```
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<AssignMessage name="GenerateGeocodingRequest">
        <AssignTo createNew="true" type="request">GeocodingRequest</AssignTo>
    <Set>
        <QueryParams>
            <QueryParam name="address">{request.queryparam.postalcode}</QueryParam>
            <QueryParam name="region">{request.queryparam.country}</QueryParam>
            <QueryParam name="sensor">false</QueryParam>
        </QueryParams>
        <Verb>GET</Verb>
    </Set>
    <AssignVariable>
        <Name>PostalCode</Name>
        <Ref>request.queryparam.postalcode</Ref>
    </AssignVariable>
    <AssignVariable>
        <Name>Country</Name>
        <Ref>request.queryparam.country</Ref>
    </AssignVariable>
</AssignMessage>
```


Now, recall that the `PostalCode` variable is used in the `<Request>` element of the Service Callout policy:


```
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<ServiceCallout name="ExecuteGeocodingRequest">
    <Request variable="PostalCode"/>
    <Response>GeocodingResponse</Response>
    <HTTPTargetConnection>
        <URL>http://maps.googleapis.com/maps/api/geocode/json</URL>
    </HTTPTargetConnection>
</ServiceCallout>
```


Because `PostalCode` is not of type message (it's a string in this example), you receive the error code: `steps.servicecallout.RequestVariableNotMessageType`.


### Resolution

Ensure that the variable set in the `<Request>` element in the failed Service Callout policy is a [message](https://docs.apigee.com/api-platform/reference/variables-reference#messagevariables) type flow variable that exists or alternatively you can create a new message type variable directly in the Service Callout policy (as explained in the [policy documentation](/api-platform/reference/policies/service-callout-policy#request)) and use it.

To correct the policy, you have to modify the `<Request>` element to specify an existing or new variable that is of type message. For example, the variable `GeocodingRequest` that was set in the Assign Message policy is of type message, and would work just fine in the Service Callout policy. For example:


```
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<ServiceCallout name="ExecuteGeocodingRequest">
    <Request variable="GeocodingRequest"/>
    <Response>GeocodingResponse</Response>
    <HTTPTargetConnection>
        <URL>http://maps.googleapis.com/maps/api/geocode/json</URL>
    </HTTPTargetConnection>
</ServiceCallout>
```



## RequestVariableNotRequestMessageType



### Error code


```
steps.servicecallout.RequestVariableNotRequestMessageType
```



### Error response body


<pre>
{
    "fault": {
        "faultstring": "ServiceCallout[<b><i>policy_name</i></b>]: request variable [<b><i>variable_name</b></i>] value is not of type Request Message",
        "detail": {
            "errorcode": "steps.servicecallout.RequestVariableNotRequestMessageType"
        }
    }
}
</pre>



### Cause

This error occurs if a variable specified in the `<Request>` element of the [Service Callout policy](https://docs.apigee.com/api-platform/reference/policies/service-callout-policy#request) is not of type [request message](https://docs.apigee.com/api-platform/reference/variables-reference#messagevariables). If the variable is a response message type, a string, or any other type, then you'll see this error.

[Message](https://docs.apigee.com/api-platform/reference/variables-reference#messagevariables) type variables represent entire HTTP requests and responses. The built-in Edge flow variables `request`, `response`, and `message` are of type message. To learn more about message variables, see the [Variables reference](https://docs.apigee.com/api-platform/reference/variables-reference#messagevariables). 


### Diagnosis



1.  Identify the Service Callout policy where the error occurred and the name of the variable whose type is incorrect. You can find both of these items in the `faultstring` element of the error response. For example, in the following `faultstring`, the policy name is `ExecuteGeocodingRequest` and the variable is `var_response`:
 
    <code> "faultstring": "ServiceCallout[<strong>ExecuteGeocodingRequest</strong>]: request variable <strong>var_response</strong> value is not of type Message"</code>

1.  In the failed Service Callout policy XML, verify that the name of the variable set in the `<Request>` element matches the variable name identified in the fault string (step #1 above). For example, the following policy specifies a request variable named `var_response`, which matches what's in the `faultstring`: 


    ```
    <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
    <ServiceCallout name="ExecuteGeocodingRequest">
        <Request variable="var_response"/>
        <Response>GeocodingResponse</Response>
        <HTTPTargetConnection>
            <URL>http://maps.googleapis.com/maps/api/geocode/json</URL>
        </HTTPTargetConnection>
    </ServiceCallout>
    ```


1.  Determine if the variable is of request message type or not:
    1.  Locate the code within the API Proxy bundle, where the variable was defined first. 
    1.  In most cases, you'll find that the problem variable is created and populated in another policy that executes before the Service Callout policy. For example, the Assign Message policy is commonly used to create and populate variables in an API proxy flow.
    1.  Once you figure out the policy in which the variable is defined and populated first, you need to determine the type of that variable as follows:
        * Check the value of the `type` attribute (if present).
        * If the `type` attribute is not present, then the variable is considered to be a string.
    1.  If the variable's type is not of **request message** type, then that's the cause of the error. You can learn about common variables and their types in the [Variables reference](https://docs.apigee.com/api-platform/reference/variables-reference).

As an example, assume the `var_response` variable referenced in the Service Callout policy was created in the following Assign Message policy. Note that `var_response` is given the type `response`. Therefore, the type of the `var_response` variable is response message.


```
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<AssignMessage name="GenerateGeocodingRequest">
        <AssignTo createNew="true" type="request">GeocodingRequest</AssignTo>
    <AssignTo createNew="true" type="response">var_response</AssignTo>
    <Set>
        <QueryParams>
            <QueryParam name="address">{request.queryparam.postalcode}</QueryParam>
            <QueryParam name="region">{request.queryparam.country}</QueryParam>
            <QueryParam name="sensor">false</QueryParam>
        </QueryParams>
        <Verb>GET</Verb>
    </Set>
    <AssignVariable>
        <Name>PostalCode</Name>
        <Ref>request.queryparam.postalcode</Ref>
    </AssignVariable>
    <AssignVariable>
        <Name>Country</Name>
        <Ref>request.queryparam.country</Ref>
    </AssignVariable>
</AssignMessage>
```


Recall that the `var_response` variable is used in the `<Request>` element of the Service Callout policy. 


```
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<ServiceCallout name="ExecuteGeocodingRequest">
    <Request variable="var_response"/>
    <Response>GeocodingResponse</Response>
    <HTTPTargetConnection>
        <URL>http://maps.googleapis.com/maps/api/geocode/json</URL>
    </HTTPTargetConnection>
</ServiceCallout>
```


Because `var_response` is not of type request message (its type is response message), you receive the error code: `steps.servicecallout.RequestVariableNotRequestMessageType`.


### Resolution

Ensure that the variable set in the `<Request>` element in the failed Service Callout policy is [request message](https://docs.apigee.com/api-platform/reference/variables-reference#messagevariables) type variable that exists or alternatively you can create a new request message type variable directly in the Service Callout policy (as explained in the [policy documentation](/api-platform/reference/policies/service-callout-policy#request)) and use it.

To correct the policy, you have to modify the `<Request>` element to specify an existing or new variable that is of type request message, and it will work in the Service Callout policy. For example: 



```
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<ServiceCallout name="ExecuteGeocodingRequest">
    <Request variable="GeocodingRequest"/>
    <Response>GeocodingResponse</Response>
    <HTTPTargetConnection>
        <URL>http://maps.googleapis.com/maps/api/geocode/json</URL>
    </HTTPTargetConnection>
</ServiceCallout>
```



## ExecutionFailed




### Error code


```
steps.servicecallout.ExecutionFailed
```



### Error response body


<pre>
{
    "fault": {
        "faultstring": "Execution of ServiceCallout [<b><i>policy_name</b></i>] failed. Reason: Host not reachable",
        "detail": {
            "errorcode": "steps.servicecallout.ExecutionFailed"
        }
    }
}
</pre>


or



<pre>
{
    "fault": {
        "faultstring": "Execution of ServiceCallout [<b><i>policy_name</b></i>] failed. Reason: ResponseCode [<b><i>http_code</b></i>] is treated as error",
        "detail": {
            "errorcode": "steps.servicecallout.ExecutionFailed"
        }
    }
}
</pre>



### Possible causes

The possible causes for this error are:


<table>
  <tr>
   <td><strong>Cause</strong>
   </td>
   <td><strong>Description</strong>
   </td>
  </tr>
  <tr>
   <td><a href="#cause-invalid-or-malformed-url">Invalid or malformed URL</a>
   </td>
   <td>The target URL in the Service Callout policy is malformed or has an invalid or unreachable host name.
   </td>
  </tr>
  <tr>
   <td><a href="#cause-backend-server-error">Backend server error</a>
   </td>
   <td>The backend server returns an error response of 4XX or 5XX.
   </td>
  </tr>
</table>



### <a name="cause-invalid-or-malformed-url">Cause: Invalid or malformed URL</a>

The target URL in the Service Callout policy is malformed or has an invalid or unreachable host name.


### Diagnosis



1.  Identify the Service Callout policy that caused the error. The policy name appears in the `faultstring` element of the error response. For example, in the following `faultstring`, the failed Service Callout policy's name is  `ExecuteGeocodingRequest`. 
 
    <code>"faultstring": "ServiceCallout[<strong>ExecuteGeocodingRequest</strong>]"</code>
1.  In the failed Service Callout policy, examine the `<URL>` element. If it is malformed or has an invalid or unreachable host name, then that's the cause for this error. For example, the following Service Callout policy specifies an invalid `<URL>`:

    ```
    <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
    <ServiceCallout name="ExecuteGeocodingRequest">
        <Request variable="GeocodingRequest"/>
        <Response>GeocodingResponse</Response>
        <HTTPTargetConnection>
            <URL>http://</URL>
        </HTTPTargetConnection>
    </ServiceCallout>
    ```



    The `<URL>` element only has the protocol `http://`, but does not have a valid hostname; therefore, the Service Callout policy fails with the error: `Execution of ServiceCallout ExecuteGeocodingRequest failed. Reason: Host not reachable`.



### Resolution

Ensure that the `<URL>` element in the failed Service Callout policy has a valid URL with a reachable hostname.

To correct the Service Callout policy shown above, you can modify the `<URL>` element to specify a valid URL:


```
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<ServiceCallout name="ExecuteGeocodingRequest">
    <Request variable="GeocodingRequest"/>
    <Response>GeocodingResponse</Response>
    <HTTPTargetConnection>
        <URL>http://maps.googleapis.com/maps/api/geocode/json</URL>
    </HTTPTargetConnection>
</ServiceCallout>
```



### <a name="cause-backend-server-error">Cause: Backend server error</a>

The backend server returns an error response of 4XX or 5XX.


### Diagnosis



1.  Identify the Service Callout policy that caused the error. The policy name appears in the `faultstring` element of the error response. For example, in the following `faultstring`, the failed Service Callout policy's name is   `ExecuteGeocodingRequest`.
 
    <code>"faultstring": "ServiceCallout[<strong>ExecuteGeocodingRequest</strong>]</code>
1.  Examine the `faultstring` in the error response body and check if there are any 4XX or 5XX response codes listed in the `Reason`. For example, the following faultstring clearly indicates that a response code 502 was returned from the backend server:



    <code>"faultstring": "Execution of ServiceCallout ExecuteGeocodingRequest failed. Reason: ResponseCode 502 is treated as error"</code>




### Resolution

Once you determine the error response code, you can troubleshoot this issue just like you would any 4XX or 5XX error. Refer to the [Runtime error (4XX/5XX) playbooks](https://docs.apigee.com/api-platform/troubleshoot/apigee-edge-error-messages#runtimeerrors) for instructions on troubleshooting and resolving 4XX or 5XX errors.





{% endblock %}
