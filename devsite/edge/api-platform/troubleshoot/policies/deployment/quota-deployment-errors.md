{% extends "_base.html" %} {% block title %}Quota policy deployment error troubleshooting{% endblock %} {% block body %}

<aside class="note">Was this troubleshooting playbook helpful? Please let us know
  by clicking <a href="" class="google-feedback"
   data-p="1636213" data-b="docsite" data-context="troubleshooting-feedback">
  Send Feedback 
</a>.</aside>

## InvalidQuotaInterval


### Error message

Deployment of the API proxy through either the Edge UI or Edge management API fails with this error message:


<pre>
Error Saving Revision [<var>revision_number</var>]
Invalid quota interval [<var>interval</var>] in quota policy [<var>policy_name</var>].
</pre>


**Example error message**


```
Error Saving Revision 1
Invalid quota interval 0.1 in quota policy Quota-1.
```


**Example screenshot**


<img alt="" src="/api-platform/images/quota-deploy-error-5.png">



### Cause

If the quota interval specified in the `<Interval>` element of the Quota Policy is not an integer, then the deployment of the API proxy fails.

For example, if the quota interval specified is 0.1 in the `<Interval>` element of a Quota Policy, then the deployment of the API proxy fails.


### Diagnosis



1.  Identify the Quota policy where the error occurred and the invalid quota interval. You can find this information from the error message. For example, in the following error, the policy name is `Quota-1` and the invalid quota interval is `0.1`:


  <pre>
  Error Saving Revision 1
  Invalid quota interval <var>0.1</var> in quota policy <var>Quota-1</var>.
  </pre>


1.  Verify that the value of the quota interval specified in the failed Quota policy matches with the value identified in the error message (step #1 above). For example, the following policy specifies the value of the quota interval as `0.1`, which matches what's in the error message:


  <pre>
  &lt;Quota async=&quot;false&quot; continueOnError=&quot;false&quot; enabled=&quot;true&quot; name=&quot;Quota-1&quot;&gt;
     &lt;DisplayName&gt;Quota-1&lt;/DisplayName&gt;
     &lt;Properties /&gt;
     &lt;Allow count=&quot;3&quot; /&gt;
     &lt;Interval&gt;<var>0.1</var>&lt;/Interval&gt;
     &lt;TimeUnit&gt;minute&lt;/TimeUnit&gt;
  &lt;/Quota&gt;
  </pre>



1.  If the quota interval specified is not an integer, then that's the cause of the error.

    In the example Quota Policy shown above, the value of the quota interval is 0.1, which is not an integer. Therefore, the deployment of the API Proxy fails with the error: 



  ```
  Invalid quota interval 0.1 in quota policy Quota-1.
  ```



### Resolution

Ensure that the value of the quota interval specified in the `<Interval>` element of the Quota Policy is an integer. For example:


<pre>
&lt;?xml version=&quot;1.0&quot; encoding=&quot;UTF-8&quot; standalone=&quot;yes&quot;?&gt;
&lt;Quota async=&quot;false&quot; continueOnError=&quot;false&quot; enabled=&quot;true&quot; name=&quot;Quota-1&quot;&gt;
    &lt;DisplayName&gt;Quota-1&lt;/DisplayName&gt;
    &lt;Properties/&gt;
    &lt;Allow count=&quot;3&quot;/&gt;
    &lt;Interval&gt;<var>1</var>&lt;/Interval&gt;
    &lt;TimeUnit&gt;minute&lt;/TimeUnit&gt;
&lt;/Quota&gt;
</pre>




## InvalidQuotaTimeUnit


### Error message

Deployment of the API proxy through either the Edge UI or Edge management API fails with this error message:

<pre>
Error Saving Revision [<var>revision_number</var>]
Invalid quota interval time unit [<var>time_unit</var>] in quota policy
[<var>policy_name</var>] in Revision [<var>revision_number</var>] of application
[<var>proxy_name</var>], in organization [<var>org_name</var>].
</pre>


**Example error message**


```
Error Saving Revision 1
Invalid quota interval time unit year in quota policy Quota-1 in Revision 1 of application Quota_test, in organization aprabhashankar-eval.
```


**Example screenshot**



<img alt="" src="/api-platform/images/quota-deploy-error-1.png">



### Cause

If the time unit specified in the `<TimeUnit>` element of the Quota policy is unsupported, then the deployment of the API proxy fails.

The supported time units are `minute`, `hour`, `day`, `week`, and `month`.

For example, if the unit of time is specified as `year` in the `<TimeUnit>` element of the Quota Policy,  then the deployment of the API proxy fails.


### Diagnosis



1.  Identify the Quota policy where the error occurred and the invalid time unit. You can find this information from the error message. For example, in the following error, the policy name is `Quota-1` and invalid time unit is `year`:

    <pre>
    Invalid quota interval time unit <var>year</var> in quota policy <var>Quota-1</var>
    in Revision 1 of application Quota_test, in organization aprabhashankar-eval.
    </pre>


1.  Verify that the time unit specified in `<TimeUnit>` element of the Quota policy matches with the time unit identified in the error message (step #1 above). For example, the following policy specifies the value of the quota interval as `year`, which matches what's in the error message:


  <pre>
  &lt;Quota async=&quot;false&quot; continueOnError=&quot;false&quot; enabled=&quot;true&quot; name=&quot;Quota-1&quot;&gt;
     &lt;DisplayName&gt;Quota-1&lt;/DisplayName&gt;
     &lt;Properties /&gt;
     &lt;Allow count=&quot;3&quot; /&gt;
     &lt;Interval&gt;1&lt;/Interval&gt;
     &lt;TimeUnit&gt;<var>year</var>&lt;/TimeUnit&gt;
  &lt;/Quota&gt;
  </pre>



1.  If the time unit specified in the Quota Policy is not supported, then that's the cause of the error.


    In the example Quota policy shown above, the time unit is specified as `year`
    which is unsupported. Therefore, the deployment of the API Proxy fails with the error:


    ```
    Invalid quota interval time unit year in quota policy Quota-1 in Revision 1 of application Quota_test, in organization aprabhashankar-eval.
    ```



### Resolution

Ensure that the time unit specified in the `<TimeUnit>` element of the Quota policy is supported. For example:


<pre>
&lt;?xml version=&quot;1.0&quot; encoding=&quot;UTF-8&quot; standalone=&quot;yes&quot;?&gt;
&lt;Quota async=&quot;false&quot; continueOnError=&quot;false&quot; enabled=&quot;true&quot; name=&quot;Quota-1&quot;&gt;
    &lt;DisplayName&gt;Quota-1&lt;/DisplayName&gt;
    &lt;Properties/&gt;
    &lt;Allow count=&quot;3&quot;/&gt;
    &lt;Interval&gt;1&lt;/Interval&gt;
    &lt;TimeUnit&gt;<var>month</var>&lt;/TimeUnit&gt;
&lt;/Quota&gt;
</pre>



## InvalidQuotaType


### Error message

Deployment of the API proxy through either the Edge UI or Edge management API fails with this error message:


<pre>
Error Saving Revision [<var>revision_number</var>]
No enum constant com.apigee.quota.types.QuotaType.[<var>type</var>].
</pre>


**Example error message**


```
Error Saving Revision 1
No enum constant com.apigee.quota.types.QuotaType.window.
```


**Example screenshot**



<img alt="" src="/api-platform/images/quota-deploy-error-2.png">



### Cause

If the **type** of the quota specified by the `type` attribute in the `<Quota>`
element of the Quota policy is invalid, then the deployment of the API proxy fails.

The supported quota types are `default`, `calendar`, `flexi`, and `rollingwindow`.

For example, if the type of the policy specified as `window` in the `<Quota>` element
of the Quota policy then the deployment of the API proxy fails.


### Diagnosis



1.  Identify the invalid quota type used in the Quota policy. You can find this
information from the error message. For example, in the following error, the invalid policy type is `window`:


  <pre>
  Error Saving Revision 1
  No enum constant com.apigee.quota.types.QuotaType.<var>window</var>.
  </pre>



1.  Examine all the Quota policies in the specific API Proxy where the failure has occurred. If there is any Quota policy in which the quota type specified in the `<Quota>` element matches the unsupported type identified in Step #1 above, then that's the cause of the error.

    For example, the following policy specifies the type as `window`, which matches what's in the error message:


    <pre>
    &lt;?xml version=&quot;1.0&quot; encoding=&quot;UTF-8&quot; standalone=&quot;yes&quot;?&gt;
    &lt;Quota async=&quot;false&quot; continueOnError=&quot;false&quot; enabled=&quot;true&quot; name=&quot;Quota-1&quot; type=&quot;<var>window</var>&quot;&gt;
        &lt;DisplayName&gt;Quota-1&lt;/DisplayName&gt;
        &lt;Properties/&gt;
        &lt;Allow count=&quot;3&quot;/&gt;
        &lt;Interval&gt;1&lt;/Interval&gt;
        &lt;TimeUnit&gt;minute&lt;/TimeUnit&gt;
        &lt;StartTime&gt;2017-7-16 12:00:00&lt;/StartTime&gt;
        &lt;MessageWeight ref=&quot;messageWeight&quot;/&gt;
    &lt;/Quota&gt;
    </pre>

    Since the type attribute is set as `window`, which is unsupported, the deployment of the API Proxy fails with the error: 


    ```
    No enum constant com.apigee.quota.types.QuotaType.window.
    ```



### Resolution

Ensure that the quota type specified by the `type` attribute in the `<Quota>` element of the Quota policy is supported. For example:


<pre>
&lt;?xml version=&quot;1.0&quot; encoding=&quot;UTF-8&quot; standalone=&quot;yes&quot;?&gt;
&lt;Quota async=&quot;false&quot; continueOnError=&quot;false&quot; enabled=&quot;true&quot; name=&quot;Quota-1&quot; type=&quot;<var>rollingwindow</var>&quot;&gt;
    &lt;DisplayName&gt;Quota-1&lt;/DisplayName&gt;
    &lt;Properties/&gt;
    &lt;Allow count=&quot;3&quot;/&gt;
    &lt;Interval&gt;1&lt;/Interval&gt;
    &lt;TimeUnit&gt;minute&lt;/TimeUnit&gt;
    &lt;StartTime&gt;2017-7-16 12:00:00&lt;/StartTime&gt;
    &lt;MessageWeight ref=&quot;messageWeight&quot;/&gt;
&lt;/Quota&gt;
</pre>


## InvalidStartTime


### Error message

Deployment of the API proxy through either the Edge UI or Edge management API fails with this error message:


<pre>
Error Saving Revision [<var>revision_number</var>]
Invalid Starttime:[<var>start_time</var>]; Start Time should be of the format yyyy-MM-dd HH:mm:ss.
</pre>


**Example error message**


```
Error Saving Revision 1
Invalid Starttime:7-16-2017 12:00:00; Start Time should be of the format yyyy-MM-dd HH:mm:ss.
```


**Example screenshot**



<img alt="" src="/api-platform/images/quota-deploy-error-3.png">



### Cause

If the format of the time specified in the `<StartTime>` element of the Quota policy is invalid, then the deployment of the API proxy fails.

The valid format is `yyyy-MM-dd HH:mm:ss`, which is the [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) date and time format.

For example, if the time specified in the  `<StartTime>` element of the Quota policy is `7-16-2017 12:00:00` then the deployment of the API proxy fails.


### Diagnosis



1.  Identify the invalid start time specified in the Quota policy. You can find this information from the error message. For example, in the following error, the invalid start time is `7-16-2017 12:00:00` 

    <pre>
    Invalid Starttime:<var>7-16-2017 12:00:00</var>; Start Time should be of the format yyyy-MM-dd HH:mm:ss.
    </pre>



1.  Examine all the Quota policies in the specific API Proxy where the failure has occurred. If there is any Quota policy in which the value specified in the `<StartTime>` element matches the invalid start time identified in Step #1 above, then that's the cause of the error.

    For example, the following policy specifies the type as `7-16-2017 12:00:00`, which matches what's in the error message:


    <pre>
    &lt;?xml version=&quot;1.0&quot; encoding=&quot;UTF-8&quot;?&gt;
    &lt;Quota async=&quot;false&quot; continueOnError=&quot;false&quot; enabled=&quot;true&quot; name=&quot;Quota-1&quot; type=&quot;calendar&quot;&gt;
       &lt;DisplayName&gt;Quota-1&lt;/DisplayName&gt;
       &lt;Properties /&gt;
       &lt;Allow count=&quot;3&quot; /&gt;
       &lt;Interval&gt;1&lt;/Interval&gt;
       &lt;TimeUnit&gt;minute&lt;/TimeUnit&gt;
       &lt;StartTime&gt;<var>7-16-2017 12:00:00</var>&lt;/StartTime&gt;
    &lt;/Quota&gt;
    </pre>

    Since the value set for `<StartTime>` is set as `7-16-2017 12:00:00`, which
    is not according to the required date/time format, the deployment of the API Proxy fails with the error:


    ```
    Invalid Starttime:7-16-2017 12:00:00; Start Time should be of the format yyyy-MM-dd HH:mm:ss.
    ```



### Resolution

Ensure that the format of the start time specified in the `<StartTime>` element of the Quota policy is valid according to the required format `yyyy-MM-dd HH:mm:ss`. For example:


<pre>
&lt;?xml version=&quot;1.0&quot; encoding=&quot;UTF-8&quot; standalone=&quot;yes&quot;?&gt;
&lt;Quota async=&quot;false&quot; continueOnError=&quot;false&quot; enabled=&quot;true&quot; name=&quot;Quota-1&quot; type=&quot;calendar&quot;&gt;
    &lt;DisplayName&gt;Quota-1&lt;/DisplayName&gt;
    &lt;Properties/&gt;
    &lt;Allow count=&quot;3&quot;/&gt;
    &lt;Interval&gt;1&lt;/Interval&gt;
    &lt;TimeUnit&gt;minute&lt;/TimeUnit&gt;
    &lt;StartTime&gt;<var>2017-7-16 12:00:00</var>&lt;/StartTime&gt;
&lt;/Quota&gt;
</pre>



## StartTimeNotSupported


### Error message

Deployment of the API proxy through either the Edge UI or Edge management API fails with this error message:


<pre>
Error Saving Revision [<var>revision_number</var>]
Starttime is not supported for quotatype [<var>quota_type</var>]. Starttime is supported only for calendar based type.
</pre>


**Example error message**


```
Error Saving Revision 1
Starttime is not supported for quotatype flexi. Starttime is supported only for calendar based type.
```


**Example screenshot**


<img alt="" src="/api-platform/images/quota-deploy-error-4.png">



### Cause

If the `<StartTime>` element is specified in a Quota policy whose quota type is not  calendar type, then the deployment of the API proxy fails.

The `<StartTime>` element is supported only for the  `calendar` quota type. 

For example, if the `type` attribute is set to `flexi` or `rolling window` in the `<Quota>` element of the Quota policy, then the deployment of the API proxy fails.


### Diagnosis



1.  Identify the quota type specified in the failing Quota policy. You can find this information from the error message. For example, in the following error, the invalid start time is `flexi` 

    <pre>
    Starttime is not supported for quotatype <var>flexi</var>. Starttime is
    supported only for calendar based type.
    </pre>


1.  Examine all the Quota policies in the specific API Proxy where the failure has occurred. If there is any Quota policy in which the type attribute specified matches the quota type identified in Step #1 above and `<StartTime>` element is specified, then that's the cause of the error. 

    For example, the following policy specifies the quota type as `flexi`, which matches what's in the error message and also specifies `<StartTime>` element:


    <pre>
    &lt;?xml version=&quot;1.0&quot; encoding=&quot;UTF-8&quot; standalone=&quot;yes&quot;?&gt;
    &lt;Quota async=&quot;false&quot; continueOnError=&quot;false&quot; enabled=&quot;true&quot; name=&quot;Quota-1&quot; type=&quot;flexi&quot;&gt;
        &lt;DisplayName&gt;Quota-1&lt;/DisplayName&gt;
        &lt;Properties/&gt;
        &lt;Allow count=&quot;3&quot;/&gt;
        &lt;Interval&gt;1&lt;/Interval&gt;
        &lt;TimeUnit&gt;minute&lt;/TimeUnit&gt;
        <var>&lt;StartTime&gt;2017-7-16 12:00:00&lt;/StartTime&gt;</var>
    &lt;/Quota&gt;
    </pre>



    Since the `<StartTime>` element is specified in the Quota Policy whose quota type is specified as `flexi`, the deployment of the API Proxy fails with the error:


    <pre>
    Starttime is not supported for quotatype <var>flexi</var>. Starttime is supported only for calendar based type.
    </pre>



### Resolution

Ensure that the `<StartTime>` element is not specified when the quota type indicated by the `type` attribute in the `<Quota>` element is `flexi` or `rolling window`. For example: 


<pre>
&lt;?xml version=&quot;1.0&quot; encoding=&quot;UTF-8&quot; standalone=&quot;yes&quot;?&gt;
&lt;Quota async=&quot;false&quot; continueOnError=&quot;false&quot; enabled=&quot;true&quot; name=&quot;Quota-1&quot; <var>type=&quot;flexi</var>&quot;&gt;
    &lt;DisplayName&gt;Quota-1&lt;/DisplayName&gt;
    &lt;Properties/&gt;
    &lt;Allow count=&quot;3&quot;/&gt;
    &lt;Interval&gt;1&lt;/Interval&gt;
    &lt;TimeUnit&gt;minute&lt;/TimeUnit&gt;
&lt;/Quota&gt;
</pre>










## InvalidTimeUnitForDistributedQuota


### Error message

Deployment of the API proxy through either the Edge UI or Edge management API fails with this error message:


<pre>
Error Saving Revision [<var>revision number</var>]
Invalid timeunit second for distributed quota.
</pre>


**Example error message**


```
Error Saving Revision 1
Invalid timeunit second for distributed quota.
```


**Example screenshot**



<img alt="" src="/api-platform/images/quota-deploy-error-7.png">



### Cause

If the `<Distributed>` element is set to `true` and the `<TimeUnit>` element is set to `second` then the deployment of the API proxy fails. The timeunit `second` is invalid for a distributed quota. 

When the `Distributed` element is set to `true`, then the policy should maintain a central counter and continuously synchronize it across all Message Processors. Therefore, it would be difficult to synchronize and also verify that the number of requests did not exceed the specified quota in a short interval of time such as seconds. For this reason, the timeunit `second` is considered invalid for distributed quota.


### Diagnosis

Examine all the Quota policies in the specific API Proxy where the failure has occurred. If there is any Quota policy with a `<TimeUnit>` element set to `second` and the `<Distributed>` element is set to `true`, then that's the cause of the error. 

For example, the below policy has a `<TimeUnit>` element set to `second` and the `<Distributed>` element is set to `true`.


<pre class="prettyprint">
&lt;?xml version=&quot;1.0&quot; encoding=&quot;UTF-8&quot; standalone=&quot;yes&quot;?&gt;
&lt;Quota async=&quot;false&quot; continueOnError=&quot;false&quot; enabled=&quot;true&quot; name=&quot;CheckQuota&quot; type=&quot;calendar&quot;&gt;
    &lt;DisplayName&gt;CheckQuota&lt;/DisplayName&gt;
    &lt;Properties/&gt;
    &lt;Allow count=&quot;30&quot;/&gt;
    &lt;Interval&gt;1&lt;/Interval&gt;
    <b>&lt;TimeUnit&gt;second&lt;/TimeUnit&gt;</b>
    &lt;StartTime&gt;2018-8-05 12:00:00&lt;/StartTime&gt;
    <b>&lt;Distributed&gt;true&lt;/Distributed&gt;</b>
    &lt;Synchronous&gt;false&lt;/Synchronous&gt;
&lt;/Quota&gt;
</pre>



### Resolution

Ensure that the `<TimeUnit>` element is never set to `second` when the `<Distributed>` element is set to true. The `<TimeUnit>` element can be set to any of the other allowed values - `minute, hour, day, week, `or` month`. For example: 


<pre class="prettyprint">
&lt;Quota async=&quot;false&quot; continueOnError=&quot;false&quot; enabled=&quot;true&quot; name=&quot;CheckQuota&quot; type=&quot;calendar&quot;&gt;
    &lt;DisplayName&gt;CheckQuota&lt;/DisplayName&gt;
    &lt;Properties/&gt;
    &lt;Allow count=&quot;30&quot;/&gt;
    &lt;Interval&gt;1&lt;/Interval&gt;
    <b>&lt;TimeUnit&gt;hour&lt;/TimeUnit&gt;</b>
    &lt;StartTime&gt;2018-8-05 12:00:00&lt;/StartTime&gt;
    <b>&lt;Distributed&gt;true&lt;/Distributed&gt;</b>
    &lt;Synchronous&gt;false&lt;/Synchronous&gt;
&lt;/Quota&gt;
</pre>


## InvalidSynchronizeIntervalForAsyncConfiguration


### Error message

Deployment of the API proxy through either the Edge UI or Edge management API fails with this error message:


<pre class="prettyprint">
Error Saving Revision [<var>revision number</var>]
SyncIntervalInSeconds should be a value greater than zero.
</pre>


**Example error message**


```
Error Saving Revision 1
SyncIntervalInSeconds should be a value greater than zero.
```


**Example screenshot**



<img alt="" src="/api-platform/images/quota-deploy-error-9.png">



### Cause

If the value specified for the `<SyncIntervalInSeconds>` element within the `<AsynchronousConfiguration>` element in a Quota policy is less than zero, then the deployment of the API proxy fails. 


### Diagnosis

Examine all the Quota policies in the specific API Proxy where the failure has occurred. If there is any Quota policy in which the `<SyncIntervalInSeconds>` element is set to a value less than zero, within the `<AsynchronousConfiguration>` element, then that's the cause of the error. 

For example, the below policy has a negative value specified for the `<SyncIntervalInSeconds>` element:


<pre class="prettyprint">
&lt;?xml version=&quot;1.0&quot; encoding=&quot;UTF-8&quot; standalone=&quot;yes&quot;?&gt;
&lt;Quota async=&quot;false&quot; continueOnError=&quot;false&quot; enabled=&quot;true&quot; name=&quot;Quota_AsyncConfig&quot; type=&quot;calendar&quot;&gt;
    &lt;DisplayName&gt;Quota_AsyncConfig&lt;/DisplayName&gt;
    &lt;Properties/&gt;
    &lt;Allow count=&quot;3&quot;/&gt;
    &lt;Interval&gt;1&lt;/Interval&gt;
    &lt;TimeUnit&gt;minute&lt;/TimeUnit&gt;
    &lt;StartTime&gt;2017-7-16 12:00:00&lt;/StartTime&gt;
    &lt;Distributed&gt;true&lt;/Distributed&gt;
    &lt;Synchronous&gt;false&lt;/Synchronous&gt;
    &lt;AsynchronousConfiguration&gt;
        <b>&lt;SyncIntervalInSeconds&gt;-1&lt;/SyncIntervalInSeconds&gt;</b>
    &lt;/AsynchronousConfiguration&gt;
&lt;/Quota&gt;
</pre>



### Resolution

Ensure that you always specify a positive integer for the   `<SyncIntervalInSeconds>` element within the `<AsynchronousConfiguration>` element in a Quota policy. For example: 


<pre class="prettyprint">
&lt;?xml version=&quot;1.0&quot; encoding=&quot;UTF-8&quot; standalone=&quot;yes&quot;?&gt;
&lt;Quota async=&quot;false&quot; continueOnError=&quot;false&quot; enabled=&quot;true&quot; name=&quot;Quota_AsyncConfig&quot; type=&quot;calendar&quot;&gt;
    &lt;DisplayName&gt;Quota_AsyncConfig&lt;/DisplayName&gt;
    &lt;Properties/&gt;
    &lt;Allow count=&quot;3&quot;/&gt;
    &lt;Interval&gt;1&lt;/Interval&gt;
    &lt;TimeUnit&gt;minute&lt;/TimeUnit&gt;
    &lt;StartTime&gt;2017-7-16 12:00:00&lt;/StartTime&gt;
    &lt;Distributed&gt;true&lt;/Distributed&gt;
    &lt;Synchronous&gt;false&lt;/Synchronous&gt;
    &lt;AsynchronousConfiguration&gt;
        <b>&lt;SyncIntervalInSeconds&gt;5&lt;/SyncIntervalInSeconds&gt;</b>
    &lt;/AsynchronousConfiguration&gt;
&lt;/Quota&gt;
</pre>





## InvalidAsynchronizeConfigurationForSynchronousQuota

### Error message

Deployment of the API proxy through either the Edge UI or Edge management API fails with this error message:

<pre>
Error Saving Revision [<var>revision number</var>]
AsynchronousConfiguration is not valid for synchronous quota.
</pre>

**Example error message**

```
Error Saving Revision 2
AsynchronousConfiguration is not valid for synchronous quota.
```

**Example screenshot**

<img alt="" src="/api-platform/images/quota-deploy-error-8.png">


### Cause

If the value of the `<Synchronous>` element is set to `true` in a Quota policy, which also has asynchronous configuration defined using the `<AsynchronousConfiguration>` element, then the deployment of the API proxy fails.


### Diagnosis

Examine all the Quota policies in the specific API Proxy where the failure has occurred. If there is any Quota policy where the `<Synchronous>` element is set to `true` and if it also has an `<AsynchronousConfiguration>` element defined, then that is the cause of the error.

For example, the below policy has a `<Synchronous>` element that is set to `true` and also has an `<AsynchronousConfiguration>` element defined:


<pre class="prettyprint">
&lt;?xml version=&quot;1.0&quot; encoding=&quot;UTF-8&quot; standalone=&quot;yes&quot;?&gt;
&lt;Quota async=&quot;false&quot; continueOnError=&quot;false&quot; enabled=&quot;true&quot; name=&quot;Quota_AsyncConfig&quot; type=&quot;calendar&quot;&gt;
    &lt;DisplayName&gt;Quota_AsyncConfig&lt;/DisplayName&gt;
    &lt;Properties/&gt;
    &lt;Allow count=&quot;3&quot;/&gt;
    &lt;Interval&gt;1&lt;/Interval&gt;
    &lt;TimeUnit&gt;minute&lt;/TimeUnit&gt;
    &lt;StartTime&gt;2017-7-16 12:00:00&lt;/StartTime&gt;
    &lt;Distributed&gt;true&lt;/Distributed&gt;
    <b>&lt;Synchronous&gt;true&lt;/Synchronous&gt;
    &lt;AsynchronousConfiguration&gt;
     &lt;SyncIntervalInSeconds&gt;1&lt;/SyncIntervalInSeconds&gt;
    &lt;/AsynchronousConfiguration&gt;</b>
&lt;/Quota&gt;
</pre>



### Resolution

Ensure that there is no asynchronous configuration defined using the `<AsynchronousConfiguration>` element if the `<Synchronous>` element is set to `true` in a Quota policy. 

The above example can be corrected by removing the `<AsynchronousConfiguration>` section as shown below:


<pre class="prettyprint">
&lt;?xml version=&quot;1.0&quot; encoding=&quot;UTF-8&quot; standalone=&quot;yes&quot;?&gt;
&lt;Quota async=&quot;false&quot; continueOnError=&quot;false&quot; enabled=&quot;true&quot; name=&quot;Quota_AsyncConfig&quot; type=&quot;calendar&quot;&gt;
    &lt;DisplayName&gt;Quota_AsyncConfig&lt;/DisplayName&gt;
    &lt;Properties/&gt;
    &lt;Allow count=&quot;3&quot;/&gt;
    &lt;Interval&gt;1&lt;/Interval&gt;
    &lt;TimeUnit&gt;minute&lt;/TimeUnit&gt;
    &lt;StartTime&gt;2017-7-16 12:00:00&lt;/StartTime&gt;
    &lt;Distributed&gt;true&lt;/Distributed&gt;
 <b>&lt;Synchronous&gt;true&lt;/Synchronous&gt;</b>
&lt;/Quota&gt;
</pre>



{% endblock %}
