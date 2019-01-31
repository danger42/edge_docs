{% extends "_base.html" %} {% block title %}Reset Quota runtime error troubleshooting{% endblock %} {% block body %}

<aside class="note">Was this troubleshooting playbook helpful? Please let us know
  by clicking <a href="" class="google-feedback"
   data-p="1636213" data-b="docsite" data-context="troubleshooting-feedback">
  Send Feedback 
</a>.</aside>


## InvalidRLPolicy


### Error code


```
policies.resetquota.InvalidRLPolicy
```


### Error response body



<pre class="prettyprint">
{
    "fault": {
        "faultstring": "Invalid rate limit policy <var>quota_policy_name</var>",
        "detail": {
            "errorcode": "policies.resetquota.InvalidRLPolicy"
        }
    }
}
</pre>



###Example error message


```
{
    "fault": {
        "faultstring": "Invalid rate limit policy MyQuotaPolicy",
        "detail": {
            "errorcode": "policies.resetquota.InvalidRLPolicy"
        }
    }
}
```



### Cause

This error occurs if the Quota policy specified in the `<Quota>` element of the
Reset Quota policy is not defined in the API proxy. The `<Quota>` element is mandatory
and it identifies the target Quota policy whose counter should be updated through
the Reset Quota policy. This target Quota policy must be created and defined within
the same API proxy and has to be available during the flow.

For example, let's say the` <Quota>` element is defined as seen below, but if
```MyQuotaPolicy``` is not defined in the API Proxy then you get the above error.


```
<Quota name="MyQuotaPolicy">
```



### Diagnosis



1.  Identify the invalid Quota policy name specified in the Reset Quota policy.
You can find the name of the Quota policy from the faultstring. For example, in
the following faultstring, the Quota policy name is `MyQuotaPolicy:`


    ```
    "faultstring": "Invalid rate limit policy MyQuotaPolicy"
    ```


1.  Examine each of the Reset Quota policies in the API Proxy where the failure
has occurred. Identify the specific Reset Quota policy where the Quota policy
specified within the mandatory element `<Quota>` matches with the name identified
in Step 1 above.

    For example, the following Reset Quota policy specifies a Quota policy named
    `MyQuotaPolicy`, which matches what's in the faultstring:



    ```
    <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
        <ResetQuota async="false" continueOnError="false" enabled="true" name="Reset_Quota_Demo">
        <DisplayName>Reset_Quota_Demo</DisplayName>
        <Properties/>
        <Quota name="MyQuotaPolicy">
           <Identifier name="identifierName" ref="request.header.identifier">
                <Allow>100</Allow>
            </Identifier>
        </Quota>
    </ResetQuota>

    ```



1.  Determine if this Quota policy is created and defined within the same API proxy.
If the Quota policy doesn't exist within the same API proxy, then that's the cause
of the error.

    In the example shown above, the Quota policy `MyQuotaPolicy` is supposed to
    exist in the flow before the Reset Quota policy ```Reset_Quota_Demo``` executes.


    Because the Quota Policy `MyQuotaPolicy` doesn't exist in the flow, you will
    receive the error code:



```
"errorcode": "policies.resetquota.InvalidRLPolicy"
```



### Resolution

Ensure that the Quota Policy declared in the `<Quota>` element is defined in the
API proxy. This target Quota policy has to be available when the Reset Quota policy
executes.



## FailedToResolveAllowCountRef


### Error code


```
policies.resetquota.FailedToResolveAllowCountRef
```



### Error response body


<pre class="prettyprint">
Failed to resolve allow count reference <var>reference_name</var> for identifier <var>identifier_name</var>
in ResetQuotaStepDefinition:<var>policy_name</var>;API Proxy:<var>proxy_name</var>;Revision:
<var>revision_number</var>;Environment:<var>env_name</var>;Organization:<var>org_name</var>
</pre>



###Example error message


```
Failed to resolve allow count reference request.header.allowcount for identifier
identifierName in ResetQuotaStepDefinition:ResetQuota;API Proxy:Reset_Quota;
Revision:10;Environment:test;Organization:demo
```


Note: The above error message is not sent as a response to the client. It is
seen only in the Edge Trace tool as an error from the Reset Quota policy. A sample screenshot
is shown below.



<img class="screenshot" width="80%" alt="" src="/api-platform/images/reset-quota-runtime-error.png">



The error returned to the client will be simply: `policies.ratelimit.QuotaViolation`.


### Cause

This error occurs if the reference to the variable containing the allow count in
the `<Allow>` element of the policy cannot be resolved to a value. This
element is mandatory and specifies the amount to decrease the quota counter.


### Diagnosis



1.  Identify the Reset Quota policy where the error has occurred and the name of
the reference variable that cannot be resolved. You can find both of these items
in the error response.

    For example, in the following faultstring, the policy name is `ResetQuota` and
    the reference is `request.header.allowcount`:


    ```
    Failed to resolve allow count reference request.header.allowcount for identifier
    identifierName in ResetQuotaStepDefinition:ResetQuota;API Proxy:Reset_Quota;
    Revision:10;Environment:test;Organization:demo
    ```


1.  In the failed Reset Quota policy XML, verify that the name of the reference
variable used matches the reference name identified in the error response (Step 1 above).

    For example, the following policy sets the element with the reference named
    `request.header.allowcount`, which matches what's in the error:


    ```
    <ResetQuota async="false" continueOnError="false" enabled="true" name="ResetQuota">
        <DisplayName>ResetQuota</DisplayName>
        <Properties/>
        <Quota name="MyQuotaPolicy">
            <Identifier name="identifierName">
                <Allow ref="request.header.allowcount"/>
            </Identifier>
        </Quota>
    </ResetQuota>
    ```


1.  Determine if the reference variable is defined and available in the flow in
which the Reset Quota policy executes.
1.  If the variable is either:
    1.  out of scope (not available in the specific flow where the policy is being executed) or
    1.  can't be resolved (is not defined)

    then that's the cause of the error.

    In the example shown above, the value of the allowed count in the `<Allow>`element
    is supposed to be retrieved from the request header named **allowcount**. However,
    Edge is unable to resolve the **request.header.allowcount**. This happens if the
    header **allowcount** is not passed as part of the API request.

    Here's a sample API request that does not pass the header **allowcount** as part
    of the request:

    ```
    curl -v http://demo-eval-test.apigee.net/v1/reset_quota -H "Content-Type: application/json"
    ```

    Because the header **allowcount** is not passed as part of the request, the variable
    **request.header.allowcount** used in the element `<Allow>` in the above Reset
    Quota policy is undefined and therefore cannot be resolved. So you will receive the
    error response:

    ```
    Failed to resolve allow count reference request.header.allowcount for identifier
    identifierName in ResetQuotaStepDefinition:ResetQuota;API Proxy:Reset_Quota;
    Revision:10;Environment:test;Organization:demo
    ```



### Resolution

Ensure that the variable referenced in the `<Allow>` element exists/is defined
and available in the specific flow, where the Reset Quota policy executes.

To correct the example shown above, you can modify the request to include the
**allowcount** header as shown below:


```
curl -v http://demo-eval-test.apigee.net/v1/reset_quota -H "Content-Type: application/json" -H "allowcount:30"
```




## FailedToResolveRLPolicy


### Error code


```
policies.resetquota.FailedToResolveRLPolicy
```



### Error response body


```
{
    "fault": {
        "faultstring": "Failed to resolve rate limit policy",
        "detail": {
            "errorcode": "policies.resetquota.FailedToResolveRLPolicy"
        }
    }
}
```


###Example error message


```
{
    "fault": {
        "faultstring": "Failed to resolve rate limit policy",
        "detail": {
            "errorcode": "policies.resetquota.FailedToResolveRLPolicy"
        }
    }
}
```



### Cause

This error occurs if the variable referenced by the `ref` attribute in the
`<Quota>` element cannot be resolved.

For example, if the `ref` attribute is set as `request.header.quotapolicy` in the
`<Quota>` element but is not available in the API proxy flow, then you get the
above error.


```
<Quota ref="request.header.quotapolicy">
```



### Diagnosis



1.  Examine each of the Reset Quota policies in the API Proxy where the failure
occurred and identify the Reset Quota policy where the name of the reference
variable specified in `<Quota>` element cannot be resolved properly.

    Note: You could determine the specific Reset Quota policy that is failing
    by capturing the trace for the failing API request.

1.  Determine if the variable is defined and available in the flow in which the
Reset Quota policy executes.
1.  If the variable is either:
    1.  out of scope (not available in the specific flow where the policy executes) or
    1.  can't be resolved (is not defined)

    then that's the cause of the error.


    In the example shown below, the name of the target Quota policy is supposed to be
    retrieved from the request header named **quotapolicy**. However, Edge is unable
    to resolve the **request.header.quotapolicy**. This happens if the header **quotapolicy**
    is not passed as part of the API request.


    ```
    <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
    <ResetQuota async="false" continueOnError="false" enabled="true" name="Reset_Quota_Demo">
        <DisplayName>Reset_Quota_Demo</DisplayName>
        <Properties/>
        <Quota ref="request.header.quotapolicy">
            <Identifier name="identifierName" ref="request.header.identifier">
                <Allow>100</Allow>
            </Identifier>
        </Quota>
    </ResetQuota>
    ```


    Here's a sample API request that does not pass the header **quotapolicy** as
    part of the request:


    ```
    curl -v http://demo-eval-test.apigee.net/v1/reset_quota -H "Content-Type: application/json"
    ```


    Because the header **quotapolicy** is not passed as part of the request, the reference
    **request.header.quotapolicy** used in the element `<Quota>` in the above Reset
    Quota policy is undefined and cannot be resolved.  As a result, you receive
    the error response:


    ```
    "errorcode": "policies.resetquota.FailedToResolveRLPolicy"
    ```



### Resolution

Ensure that the attribute `ref` referenced in the `<Quota>` element resolves at
runtime and is available in the specific flow, where the Reset Quota policy executes.

To correct the example shown above, you can modify the request to include the **quotapolicy** header as shown below:


```
curl -v http://demo-eval-test.apigee.net/v1/reset_quota -H "Content-Type: application/json" -H "quotapolicy:MyQuotaPolicy"
```


{% endblock %}
