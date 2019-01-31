{% extends "_base.html" %} {% block title %}BasicAuthentication policy runtime error troubleshooting{% endblock %} {% block body %}

<aside class="note">Was this troubleshooting playbook helpful? Please let us know
  by clicking <a href="" class="google-feedback"
   data-p="1636213" data-b="docsite" data-context="troubleshooting-feedback">
  Send Feedback 
</a>.</aside>


## UnresolvedVariable


### Error code

```steps.basicauthentication.UnresolvedVariable```


### Error response body

```
{
    "fault": {
        "faultstring": "Unresolved variable : [variable_name]",
        "detail": {
            "errorcode": "steps.basicauthentication.UnresolvedVariable"
        }
    }
}
```


### Example Error Message

```
{
    "fault": {
        "faultstring": "Unresolved variable : request.header.Authorization",
        "detail": {
            "errorcode": "steps.basicauthentication.UnresolvedVariable"
        }
    }
}
```


### Cause

This error occurs if the variable containing the Base64 encoded string specified in the `<Source>` element of the [BasicAuthentication policy](/api-platform/reference/policies/basic-authentication-policy) is either:

*   Out of scope (not available in the specific flow where the policy is being executed)
*   Can't be resolved (is not defined)

For example, this error occurs if the BasicAuthentication policy has a variable specified as` request.header.Authorization` in the` <Source>` element, but the Authorization header is not passed as part of the API request. 

<aside class="note"><b>Note: </b>This error can only occur if <code>IgnoreUnresolvedVariables</code> element is set to <code>false</code>. </aside>


### Diagnosis

1.  Identify the variable used for the `<Source>` element in the BasicAuthentication policy. You can find this information in the `faultstring` element of the error response. For example, in the following `faultstring,` the variable used for  `<Source>` element is `request.header.Authorization`:

  ```
  "faultstring": "Unresolved variable : request.header.Authorization"
  ```

1.  Examine all the BasicAuthentication policies in the specific API Proxy where the failure has occurred. There could be one or more BasicAuthentication policies. Identify the specific BasicAuthentication policy or policies  in which the variable specified in the `<Source>` element matches the variable name identified in the fault string (step #1 above).

  For example, the following policy sets the `<Source>` element to a variable named `request.header.Authorization`, which matches what's in the `faultstring`:


  ```
  <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
  <BasicAuthentication name="DecodeBaseAuthHeaders">
    <DisplayName>Decode Basic Authentication Header</DisplayName>
    <Operation>Decode</Operation>
    <IgnoreUnresolvedVariables>false</IgnoreUnresolvedVariables>
    <User ref="request.header.username"/>
    <Password ref="request.header.password"/>
    <Source>request.header.Authorization</Source>
  </BasicAuthentication>
  ```

1.  Determine if the variable is defined and available in the flow in which the BasicAuthentication policy is being executed.

1.  If the variable is either:
    1.  Out of scope (not available in the specific flow where the policy is being executed) 
    1.  Can't be resolved (is not defined)

    Then that's the cause for the error.


    In the example BasicAuthentication policy shown above, the variable `request.header.Authorization `was not passed as part of the request. That is, the API request was made without the Authorization header as shown below:


    <pre class="devsite-terminal">curl -v "http://<var>org</var>-<var>env</var>.apigee.net/basicauth"</pre>

    Since the variable used in the BasicAuthentication policy is not available, you receive the error code: 

    ```
    steps.basicauthentication.UnresolvedVariable
    ```


### Resolution

Ensure that the variable used in the BasicAuthentication policy is defined or passed as an input and available in the flow where the policy is being executed. 

To fix the issue with the sample BasicAuthentication policy discussed above, make an API request as shown below: 

<pre class="devsite-terminal">curl -v "http://<var>org</var>-<var>env</var>.apigee.net/basicauth" -H "Authorization: Basic YWthc2g6MTIz"</pre>


## InvalidBasicAuthenticationSource


### Error code

`steps.basicauthentication.InvalidBasicAuthenticationSource`


### Error response body


```
{
    "fault": {
        "faultstring": "Source variable : [variable_name] for basic authentication decode policy is not valid",
        "detail": {
            "errorcode": "steps.basicauthentication.InvalidBasicAuthenticationSource"
        }
    }
}
```

### Error Example Error Message

```
{
    "fault": {
        "faultstring": "Source variable : request.header.Authorization for basic authentication decode policy is not valid",
        "detail": {
            "errorcode": "steps.basicauthentication.InvalidBasicAuthenticationSource"
        }
    }
}
```


### Possible causes

The possible causes for this error are:

<table>
  <thead>
  <tr>
   <th>Cause</th>
   <th>Description</th>
  </tr>
  </thead>
  <tbody>
  <tr>
   <td>Invalid source variable
   </td>
   <td>The input source variable to BasicAuthentication Policy is not a valid Base64 encoded string.
   </td>
  </tr>
  <tr>
   <td>Header is Malformed
   </td>
   <td>The header containing the Base64 encoded string passed to BasicAuthentication policy is malformed.
   </td>
  </tr>
</table>


### Cause: Invalid source variable

This error occurs if the variable containing the Base64 encoded string specified in the `<Source>` element of the [BasicAuthentication policy](https://docs.apigee.com/api-platform/reference/policies/basic-authentication-policy) does not contain a valid value.

For example, this error occurs if the variable specified in the` <Source>` element of BasicAuthentication policy does not have a valid Base64 encoded string. 


### Diagnosis

1.  Identify the variable used for the `<Source>` element in the BasicAuthentication policy. You can find this information in the `faultstring` element of the error response. For example, in the following `faultstring,` the variable used for  `<Source>` element is `request.header.Authorization`:

    ```
    "faultstring": "Source variable : request.header.Authorization for basic authentication decode policy is not valid"
    ```


1.  Examine all the BasicAuthentication policies in the specific API Proxy where the failure has occurred. There could be one or more BasicAuthentication policies. Identify the specific BasicAuthentication policy (ies)  in which the variable specified in the `<Source>` element matches the variable name identified in the fault string (step #1 above).

    For example, the following policy sets the `<Source>` element to a variable named `request.header.Authorization`, which matches what's in the `faultstring`:

    ```
    <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
    <BasicAuthentication name="DecodeBaseAuthHeaders">
        <DisplayName>Decode Basic Authentication Header</DisplayName>
        <Operation>Decode</Operation>
        <IgnoreUnresolvedVariables>false</IgnoreUnresolvedVariables>
        <User ref="request.header.username"/>
        <Password ref="request.header.password"/>
        <Source>request.header.Authorization</Source>
    </BasicAuthentication>
    ```

1.  If the value stored in the variable specified in `<Source>` element is not a valid Base64 encoded string, then` `that's the cause for the error.

    In the example BasicAuthentication policy shown above, the variable `request.header.Authorization `that was sent by the client as a header was invalid as shown below:

    <pre class="devsite-terminal">curl -v "http://<var>org</var>-<var>env</var>.apigee.net/basicauth" -H "Authorization: Basic 23435"</pre>

    Since the variable `request.header.Authorization `has an invalid Base64 encoded string "`23435"`, you receive the error code:

    `steps.basicauthentication.InvalidBasicAuthenticationSource`


### Resolution

Ensure that the variable specified for `<Source>` element in the BasicAuthentication policy has a valid Base64 encoded string. 

To fix the issue with the sample BasicAuthentication policy discussed above, make an API request by passing a valid Base64 encoded string in the Authorization header as shown below: 

<pre class="devsite-terminal">curl -v "http://<var>org</var>-<var>env</var>.apigee.net/basicauth" -H "Authorization: Basic YWthc2g6MTIz"</pre>


### Cause: Header is Malformed/invalid 

This error occurs if the header passed to the [BasicAuthentication policy](https://docs.apigee.com/api-platform/reference/policies/basic-authentication-policy) is malformed or invalid.

For example, this error occurs if the BasicAuthentication policy has a variable specified as` request.header.Authorization` in the` <Source>` element and the header passed as part of the API request is malformed/invalid. 


### Diagnosis

1.  Identify the variable used for the `<Source>` element in the BasicAuthentication policy. You can find this information in the `faultstring` element of the error response. For example, in the following error, the variable used for  `<Source>` element is `request.header.Authorization`:

    ```
    "faultstring": "Source variable : request.header.Authorization for basic authentication decode policy is not valid"
    ```

1.  Examine all the BasicAuthentication policies in the specific API Proxy where the failure has occurred. There could be one or more BasicAuthentication policies. Identify the specific BasicAuthentication policy (ies)  in which the variable specified in the `<Source>` element matches the variable name identified in the fault string (step #1 above).

    For example, the following policy sets the `<Source>` element to a variable named `request.header.Authorization`, which matches what's in the `faultstring`:

    ```
    <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
    <BasicAuthentication name="DecodeBaseAuthHeaders">
        <DisplayName>Decode Basic Authentication Header</DisplayName>
        <Operation>Decode</Operation>
        <IgnoreUnresolvedVariables>false</IgnoreUnresolvedVariables>
        <User ref="request.header.username"/>
        <Password ref="request.header.password"/>
        <Source>request.header.Authorization</Source>
    </BasicAuthentication>
    ```

1.  If the variable is a header and is malformed or invalid, i.e., it does not have the authentication type or if the authentication type is not  "Basic", then that's the cause for the error.

  The Authorization header is of this form:

  ```
  Authorization: <Authentication type> <credentials>
  ```

    A malformed header can be passed to BasicAuthentication policy in two ways:

    **Example 1**: No Authentication type in the Header:

    <pre class="devsite-terminal">curl -v "http://<var>org</var>-<var>env</var>.apigee.net/basicauth" -H "Authorization: YWthc2g6MTIz"</pre>

    In the above example, the Authorization header does not have the Authentication type.  If this header is passed to BasicAuthentication policy shown above, you receive the error code: 

  ```
  steps.basicauthentication.InvalidBasicAuthenticationSource
  ```

    **Example 2**: Authentication type is not of type "Basic":

    <pre class="devsite-terminal">curl -v "http://<var>org</var>-<var>env</var>.apigee.net/basicauth" -H "Authorization: OAuth YWthc2g6MTIz"</pre>

    In the above example, the Authorization header does not have the Authentication type of "Basic".  If this is passed to BasicAuthentication policy shown above, you receive the error code:

  ```
  steps.basicauthentication.InvalidBasicAuthenticationSource
  ```

    Similarly, this error can also occur if the Authentication type such as Bearer, Digest Auth, etc is used in the Authorization header. 


### Resolution

Ensure that the header in which the input to the BasicAuthentication policy is passed has the Authentication type and is of type "Basic".

To fix the issue with the sample BasicAuthentication policy discussed above, make an API request by passing a valid Base64 encoded string with "Basic" Authentication type in the Authorization header as shown below: 

<pre class="devsite-terminal">curl -v "http://<var>org</var>-<var>env</var>.apigee.net/basicauth" -H "Authorization: Basic YWthc2g6MTIz"</pre>

{% endblock %}
