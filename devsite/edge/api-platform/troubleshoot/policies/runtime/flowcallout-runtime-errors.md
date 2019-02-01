{% extends "_base.html" %} {% block title %}FlowCallout policy runtime error troubleshooting{% endblock %} {% block body %}

<aside class="note">Was this troubleshooting playbook helpful? Please let us know
  by clicking <a href="" class="google-feedback"
   data-p="1636213" data-b="docsite" data-context="troubleshooting-feedback">
  Send Feedback 
</a>.</aside>

## SharedFlowNotFound


### Error code


```
flow.SharedFlowNotFound
```



### Error response body


<pre>
{
    "fault": {
        "faultstring": "Shared Flow <var>shared_flow_name</var> Not Found",
        "detail": {
            "errorcode": "flow.SharedFlowNotFound"
        }
    }
}
</pre>



### Possible Causes

The possible causes for this error are:


<table>
  <tr>
   <td><strong>Cause</strong>
   </td>
   <td><strong>Description</strong>
   </td>
  </tr>
  <tr>
   <td>

<a href="#cause_shared_flow_not_available">Shared flow not available</a>
   </td>
   <td>The shared flow does not exist.
   </td>
  </tr>
  <tr>
   <td>

<a href="#cause_shared_flow_not_deployed">Shared flow not deployed</a>
   </td>
   <td>The shared flow exists but is not deployed.
   </td>
  </tr>
</table>



### Cause: Shared flow not available

In the Flow Callout policy, if the shared flow defined within the `<SharedFlowBundle>` element does not exist in the environment, then this error occurs.

For example, If the `<SharedFlowBundle>` element in the Flow Callout policy contains a shared flow called `SharedFlow_Sample` and if this shared flow does not exist in the environment, then the error occurs.


#### Example Error Message


```
{
    "fault": {
        "faultstring": "Shared Flow SharedFlow_Sample Not Found",
        "detail": {
            "errorcode": "flow.SharedFlowNotFound"
        }
    }
}
```

#### Diagnosis


1.  Identify the shared flow which does not exist. You can find this in the `faultstring` element of the error response. For example, in the following `faultstring`, the shared flow name is `SharedFlow_Sample`:

  `"faultstring": "Shared Flow SharedFlow_Sample Not Found"`


1.  Examine all the Flow Callout policies in the specific API Proxy where the failure has occurred. Check if there is any Flow Callout policy in which `<SharedFlowBundle>` element is specified with the flow name identified in step #1 above. 

    For example, the following policy specifies the `<SharedFlowBundle>` as `SharedFlow_Sample`, which matches with what's in the fault string.

    ```
    <FlowCallout async="false" continueOnError="false" enabled="true" name="Auth-Flow-Callout">
       <DisplayName>Auth Flow Callout</DisplayName>
       <SharedFlowBundle>SharedFlow_Sample</SharedFlowBundle>
    </FlowCallout>
    ```


1.  In the Edge UI, check if this shared flow exists under **APIs > Shared Flows**. If it does not exist, then that's the cause of the error. (Note that in the New Edge UI, you can find Shared Flows under the Develop tab.)

    For example, in the screenshot shown below, the shared flow `SharedFlow_Sample` does not exist.


  <img alt="" src="/api-platform/images/flowcallout-runtime-error-3.png" width="75%">



  Because this shared flow does not exist, you receive the error code:


  ```
  flow.SharedFlowNotFound
  ```


#### Resolution

Ensure that the shared flow specified in the `<SharedFlowBundle>` element in the Flow Callout policy, exists and deployed in the specific environment in which the API request is executed.

You could choose one of the following ways to address the issue:



1.  Create a shared flow called `SharedFlow_Sample` as shown below and deploy it the test environment as shown below: 


  <img alt="" src="/api-platform/images/flowcallout-runtime-error-2.png" width="75%">
  


1.  Use an already existing Shared Flow that is deployed in the test environment in the Flow Callout policy.  For example, you could specify **SharedFlow_demo** in the Flow Callout policy.

  ```
  <FlowCallout async="false" continueOnError="false" enabled="true" name="Auth-Flow-Callout">
    <DisplayName>Auth Flow Callout</DisplayName>
    <SharedFlowBundle>SharedFlow_demo</SharedFlowBundle>
  </FlowCallout>
  ```

  If you make an API call to the test environment, you will no longer see the
error, because the Flow Callout is deployed to the test environment as well.


### Cause: Shared flow not deployed

If the shared flow specified in the `<SharedFlowBundle>` element of the Flow Callout policy, is not deployed, then this error occurs.

For example, If the `<SharedFlowBundle>` element in the Flow Callout policy contains a shared flow called `SharedFlow_example` which is not deployed in the specific environment in which the API request is made, then the error occurs.


#### Example Error Message


```
{
    "fault": {
        "faultstring": "Shared Flow Shared-Flow_example Not Found",
        "detail": {
            "errorcode": "flow.SharedFlowNotFound"
        }
    }
}
```



#### Diagnosis


1.  Identify the shared flow which is not deployed. You can find this in the `faultstring` element of the error response. For example, in the following `faultstring`, the shared flow name is `Shared-Flow_example`:

  `"faultstring": "Shared Flow Shared-Flow_example Not Found"`



1.  In the Edge UI, check if this shared flow is deployed.

  For example, you will notice in the below screenshot that the shared flow `SharedFlow_example` exists but` `is not deployed.


  <img alt="" src="/api-platform/images/flowcallout-runtime-error-1.png" width="75%">


  Since this shared flow is not deployed, you receive the error code: 


  ```
  flow.SharedFlowNotFound
  ```



#### Resolution

Ensure that the shared flow specified in the `<SharedFlowBundle>` element in the Flow Callout policy, exists and deployed in the specific environment in which the API request is executed.

To correct the above example, deploy the shared flow called `SharedFlow_example` in the test environment as shown below:


<img alt="" src="/api-platform/images/flowcallout-runtime-error-4.png" width="75%">


If you make an API call to the test environment, you will no longer see the
error, because the Flow Callout is deployed to the test environment as well.



{% endblock %}
