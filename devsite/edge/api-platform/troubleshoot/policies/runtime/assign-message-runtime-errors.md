{% extends "_base.html" %} {% block title %}Assign Message runtime error troubleshooting{% endblock %} {% block body %}

<aside class="note">Was this troubleshooting playbook helpful? Please let us know
  by clicking <a href="" class="google-feedback"
   data-p="1636213" data-b="docsite" data-context="troubleshooting-feedback">
  Send Feedback 
</a>.</aside>

## UnresolvedVariable


### Error code

```
steps.assignmessage.UnresolvedVariable
```

### Error response body

<pre class="prettyprint">
{
    "fault": {
        "faultstring": "AssignMessage[<var>policy_name</var>]: unable to resolve variable [<var>variable_name</var>]",
        "detail": {
            "errorcode": "steps.assignmessage.UnresolvedVariable"
        }
    }
}
</pre>


### Cause

This error occurs if a variable specified in the [Assign Message policy](/api-platform/reference/policies/assign-message-policy) is either:


*   out of scope (not available in the specific flow where the policy is being executed) or
*   can't be resolved (is not defined)

For example, this error occurs if the Assign Message policy executes in the request flow, but the `source` attribute in the `<Copy>` element is set to the `response` or `error` variable or any other custom variable that does not exist in the request flow.


### Diagnosis



1.  Identify the Assign Message Policy where the error occurred and the name of the variable that is not available. You can find both of these items in the `faultstring` element of the error response. 

  For example, in the following `faultstring`, the policy name is `googleBook` and the variable is `var`:

  ```
  "faultstring": "AssignMessage[googleBook]: unable to resolve variable var"
  ```



1.  In the failed Assign Message Policy XML, verify that the name of the variable used matches the variable name identified in the fault string (step #1 above). For example, the following policy sets the source attribute in the `<Copy>` element to a variable named `var`, which matches what's in the `faultstring`:

  ```
  <AssignMessage async="false" continueOnError="false" enabled="true" name="Assign-Message-1">
     <DisplayName>googleBook</DisplayName>
     <Properties />
     <Copy source="var">
        <Headers>
           <Header name="user-agent" />
        </Headers>
     </Copy>
     <IgnoreUnresolvedVariables>false</IgnoreUnresolvedVariables>
     <AssignTo createNew="true" transport="http" type="request">googleBookReq</AssignTo>
  </AssignMessage>
  ```


1.  Determine if the variable is defined and available in the flow in which the Assign Message policy is being executed.
1.  If the variable is either:
    1.  out of scope (not available in the specific flow where the policy is being executed) or
    1.  can't be resolved (is not defined)

  then that's the cause for the error.

  As an example, let's say the Assign Message policy shown above executes in the request flow. Check if the variable `var` is defined in any of the policies that are executed before the Assign Message policy in the request flow. If the variable has not been defined, then you will receive the error code:

  ```
  steps.assignmessage.UnresolvedVariable
  ```


### Resolution

Ensure that the variable referenced in the policy exists and is available in the specific flow, where the Assign Message policy is being executed. 

To correct the example policy shown above, you can modify the source attribute in the `<Copy>` element to be the request variable or any other custom variable of type message that exists in the request flow.

```
<AssignMessage async="false" continueOnError="false" enabled="true" name="Assign-Message-1">
   <DisplayName>googleBook</DisplayName>
   <Properties />
   <Copy source="request">
      <Headers>
         <Header name="user-agent" />
      </Headers>
   </Copy>
   <IgnoreUnresolvedVariables>false</IgnoreUnresolvedVariables>
   <AssignTo createNew="true" transport="http" type="request">googleBookReq</AssignTo>
</AssignMessage>
```




## VariableOfNonMsgType


### Error code

```
steps.assignmessage.VariableOfNonMsgType
```


### Error response body

<pre class="prettyprint">
{
    "fault": {
        "faultstring": "AssignMessage[<var>policy_name</var>]: value of variable [<var>variable</var>] is not of type Message",
        "detail": {
            "errorcode": "steps.assignmessage.VariableOfNonMsgType"
        }
    }
}
</pre>


### Cause

This error occurs if the `source` attribute in the `<Copy>` element is set to a variable which is not of type [message](/api-platform/reference/variables-reference#messagevariables). 

Message type variables represent entire HTTP requests and responses. The built-in Edge flow variables `request`, `response`, and `message` are of type message. To learn more about message variables, see the [Variables reference](/api-platform/reference/variables-reference#messagevariables).


### Diagnosis



1.  Identify the Assign Message Policy where the error occurred and the name of the variable whose type is incorrect. You can find both of these items in the `faultstring` element of the error response. For example, in the following `faultstring`, the policy name is `GenerateGeocodingRequest` and the variable is `PostalCode`:

  ```
  "faultstring": "AssignMessage[GenerateGeocodingRequest]: value of variable PostalCode is not of type Message"
  ```


1.  In the failed Assign Message Policy XML, verify that the name of the variable set in the `<Copy>` element matches the variable name identified in the fault string (step #1 above). For example, the following policy sets a source attribute to a variable named `PostalCode`, which matches what's in the `faultstring`:

  ```
  <AssignMessage name="GenerateGeocodingRequest">
     <AssignTo createNew="true" type="request">GeocodingRequest</AssignTo>
     <AssignVariable>
        <Name>PostalCode</Name>
        <Ref>request.queryparam.postalcode</Ref>
     </AssignVariable>
     <AssignVariable>
        <Name>Country</Name>
        <Ref>request.queryparam.country</Ref>
     </AssignVariable>
     <Copy source="PostalCode">
        <QueryParams>
           <QueryParam name="q" />
        </QueryParams>
     </Copy>
  </AssignMessage>
  ```


1.  Determine if this variable is of type message or not:
    1.  Locate the code within the API Proxy bundle, where the variable was defined first.
    1.  Once you figure out the policy in which the variable is defined and populated first, you need to determine the type of that variable as follows:
        1.  Check the value of the type attribute (if present).
        1.  If the type attribute is not present, then the variable is considered to be a string.
    1.  If the variable's type is non-message (such as a string), then that's the cause of the error. You can learn about common variables and their types in the [Variables reference](https://docs.apigee.com/api-platform/reference/variables-reference).

  For example,  look at the PostalCode variable in the above XML. It is assigned the value of the flow variable `request.queryparam.postalcode` in the `<AssignVariable>` element. This value is a string, because there is no type attribute present in the variable assignment.

  Now, recall that the PostalCode variable is used in the `<Copy>` element of the Assign Message policy:

  ```
  <Copy source="PostalCode">
     <QueryParams>
        <QueryParam name="PostalCode" />
     </QueryParams>
  </Copy>
  ```

  Because PostalCode is not of type message (it's a string in this example), you receive the error code: 

  ```
  steps.assignmessage.VariableOfNonMsgType
  ```


### Resolution

Ensure that the `source` attribute in the `<Copy>` element in the failed Assign Message policy is set to a [message](/api-platform/reference/variables-reference#messagevariables) type flow variable that exists.

To correct the policy, you can modify the `source` attribute in the `<Copy>` element to specify a variable that is of type message. For example, if the Assign Message policy is supposed to execute in the request flow, then you could use the message type variable `request` or any other custom variable of type message.

```
<AssignMessage name="GenerateGeocodingRequest">
   <AssignTo createNew="true" type="request">GeocodingRequest</AssignTo>
   <AssignVariable>
      <Name>PostalCode</Name>
      <Ref>request.queryparam.postalcode</Ref>
   </AssignVariable>
   <AssignVariable>
      <Name>Country</Name>
      <Ref>request.queryparam.country</Ref>
   </AssignVariable>
   <Copy source="request">
      <QueryParams>
         <QueryParam name="PostalCode" />
      </QueryParams>
   </Copy>
</AssignMessage>
```

{% endblock %}
