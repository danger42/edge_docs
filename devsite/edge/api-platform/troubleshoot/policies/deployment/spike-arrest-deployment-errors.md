{% extends "_base.html" %} {% block title %}Spike Arrest policy deployment error troubleshooting{% endblock %} {% block body %}

## InvalidAllowedRate


### Error message

Deployment of the API proxy through either the Edge UI or Edge management API fails
with this error message:


<pre class="prettyprint">
Error Saving Revision [<var>revision_number</var>]
Invalid spike arrest rate [<var>invalid_rate</var>].
</pre>

**Example error messages**

```
Error Saving Revision 1
Invalid spike arrest rate 30s.

Error Saving Revision 1
Invalid spike arrest rate 30.1ps.
```

**Example screenshots**


<img alt="" src="/api-platform/images/spike-arrest-deploy-error-1.png">


<img alt="" src="/api-platform/images/spike-arrest-deploy-error-2.png">


### Cause

If the spike arrest rate specified in the `<Rate>` element of the Spike Arrest
Policy is not an integer or if the rate does not have `ps` or `pm` as a suffix,
then the deployment of the API proxy fails.

The valid values are <code>[<strong><em>int</em></strong>]ps</code> and <code>[<strong><em>int</em></strong>]pm</code>.

For example, if the spike arrest rate specified is `30.1ps` in the `<Rate>` element
of a Spike Arrest Policy, then the deployment of the API proxy fails.  This is
because 30.1 is not an integer. 


### Diagnosis


1.  Identify the invalid spike arrest rate specified in the Spike Arrest policy.
You can find this information from the error message. For example, in the following
error, the invalid spike arrest is `30.1`:

  ```
  Error Saving Revision 1
  Invalid spike arrest rate 30.1ps.
  ```

1.  Examine all the Spike Arrest policies in the specific API proxy where the
failure has occurred. If there is any Spike Arrest policy in which the spike
arrest specified in the `<Rate>` element matches the invalid spike arrest rate
identified in Step #1 above, then that's the cause of the error.

  For example, the following policy specifies the value of  `<Rate>` as 30.1ps,
    which matches what's in the error message:


  ```
  <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
  <SpikeArrest async="false" continueOnError="false" enabled="true" name="CheckRate">
      <DisplayName>CheckRate</DisplayName>
      <Properties/>
      <Identifier ref="request.header.some-header-name"/>
      <MessageWeight ref="request.header.weight"/>
      <Rate>30.1ps</Rate>
  </SpikeArrest>
  ```

  Since the spike arrest rate specified in the `<Rate>` element is not an integer,
  the deployment of the API Proxy fails with the error:

  ```
  Invalid spike arrest rate 30.1ps.
  ```

### Resolution

Ensure that the value of the spike arrest rate specified in the `<Rate>` element
of the Spike Arrest Policy is an integer and has `ps` or `pm` as a suffix. For
example:


```
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<SpikeArrest async="false" continueOnError="false" enabled="true" name="CheckRate">
    <DisplayName>CheckRate</DisplayName>
    <Properties/>
    <Identifier ref="request.header.some-header-name"/>
    <MessageWeight ref="request.header.weight"/>
    <Rate>30ps</Rate>
</SpikeArrest>
```
    
{% endblock %}
