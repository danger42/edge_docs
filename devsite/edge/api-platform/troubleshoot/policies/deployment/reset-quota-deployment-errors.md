{% extends "_base.html" %} {% block title %}Reset Quota policy deployment error troubleshooting{% endblock %} {% block body %}

<aside class="note">Was this troubleshooting playbook helpful? Please let us know
  by clicking <a href="" class="google-feedback"
   data-p="1636213" data-b="docsite" data-context="troubleshooting-feedback">
  Send Feedback 
</a>.</aside>

## InvalidCount


### Error message

Deployment of the API proxy through either the Edge UI or Edge management API fails with this error message:


<pre class="prettyprint">
Error Saving Revision <var>revision_number</var>
Invalid count value <var>interval</var> for identifier <var>identifier_name</var> in <var>policy_name</var>.
</pre>

### Example error message

```
Error Saving Revision 1
Invalid count value 10.0 for identifier identifierName in Reset_Quota_Demo.
```

### Example screenshot

<img alt="" src="/api-platform/images/resetquota-deploy-error-1.png" width="700">


### Cause

If the count value specified in the `<Allow>` element of the Reset Quota Policy
is not an integer, then the deployment of the API proxy fails.

For example, if the quota interval specified is `10.0` in the `<Allow>` element,
then the deployment of the API proxy fails.

### Diagnosis


1.  Identify the Reset Quota policy where the error occurred and the invalid
allow count. You can find this information from the error message. For example,
in the following error, the policy name is `Reset_Quota_Demo` and the invalid allow
count is `10.0`:

  ```
  Error Saving Revision 1
  Invalid count value 10.0 for identifier identifierName in Reset_Quota_Demo.
  ```


1.  Verify that the value of the allow count specified in the failed policy matches
with the value identified in the error message (step 1 above). For example, the
following policy specifies the value of the allow count as `10.0`, which matches
with what's in the error message:

  ```
  <ResetQuota async="false" continueOnError="false" enabled="true" name="Reset_Quota_Demo">
      <DisplayName>Reset_Quota_Demo</DisplayName>
      <Properties/>
      <Quota>
          <Identifier name="identifierName" ref="request.header.identifier">
              <Allow>10.0</Allow>
          </Identifier>
      </Quota>
  </ResetQuota>
  ```

  In the example Reset Quota Policy shown above, the value of the allow count is
`10.0`, which is not an integer. Therefore, the deployment of the API Proxy fails
with the error:

  ```
  Invalid count value 10.0 for identifier identifierName in Reset_Quota_Demo.
  ```

### Resolution

Ensure that the value of the allow count specified in the `<Allow>` element of the Reset Quota Policy is an integer. To correct the example ResetQuota policy shown above, you could modify the <Allow> element to have a value of **10**.


```
<ResetQuota async="false" continueOnError="false" enabled="true" name="Reset_Quota_Demo">
    <DisplayName>Reset_Quota_Demo</DisplayName>
    <Properties/>
    <Quota>
        <Identifier name="identifierName" ref="request.header.identifier">
            <Allow>10</Allow>
        </Identifier>
    </Quota>
</ResetQuota>
```
  
  {% endblock %}

