{% extends "_base.html" %} {% block title %}Statistics Collector policy deployment error troubleshooting{% endblock %} {% block body %}

<aside class="note">Was this troubleshooting playbook helpful? Please let us know
  by clicking <a href="" class="google-feedback"
   data-p="1636213" data-b="docsite" data-context="troubleshooting-feedback">
  Send Feedback 
</a>.</aside>


## UnsupportedDatatype


### Error message

Deployment of the API proxy through either the Edge UI or Edge management API fails with this error message:

<pre>
Error Deploying Revision [<var>revision_number</var>]
StatisticsCollection [<var>datatype</var>]: Datatype Revision:[<var>revision_number</var>];APIProxy:[<var>api_proxy</var>];Organization:[<var>org_name</var>];Environment:[<var>env_name</var>] is unsupported . Context [<var>context</var>].
</pre>

**Example error message**

```
Error Saving Revision 1
StatisticsCollection char: Datatype Revision:1;APIProxy:StatCollector;Organization:aprabhashankar-eval;Environment:test is unsupported . Context {2}.
```

**Example screenshot**

<img alt="" src="/api-platform/images/stats-collector-deploy-error-1.png">


### Cause

If the type of the variable specified by the `ref` attribute in the `<Statistic>` element of the Statistics Collector policy is unsupported, then the deployment of the API proxy fails.

The supported data types are `string`, `integer`, `float`, `long`, `double`, and `boolean`.

For example, if the type of the variable is specified as `char` in the `<Statistic>` element of the Statistics Collector policy, then the deployment of the API proxy fails.


### Diagnosis


1.  Identify the Statistics Collector policy in the specific API Proxy where the failure has occurred. 
1.  Identify the unsupported data type used in the Statistics Collector policy. You can find this information from the error message. For example, in the following error, the unsupported data type is `char`:

  <pre>
  StatisticsCollection <var>char</var>: Datatype Revision:1;APIProxy:StatCollector;Organization:aprabhashankar-eval;Environment:test is unsupported . Context {2}.
  </pre>



1.  Verify that the type attribute used in the failed Statistics Collector policy matches the data type identified in the error message (step #2 above). For example, the following policy specifies the datatype as `char`, which matches what's in the error message:


  <pre>
  &lt;StatisticsCollector async=&quot;false&quot; continueOnError=&quot;false&quot; enabled=&quot;true&quot; name=&quot;Statistics-Collector-1&quot;&gt;
      &lt;DisplayName&gt;Statistics Collector 1&lt;/DisplayName&gt;
      &lt;Statistics&gt;
          &lt;Statistic name=&quot;statName&quot; ref=&quot;varName&quot <var>type=&quot;char&quot;</var>&gt;defaultStatValue&lt;/Statistic&gt;
      &lt;/Statistics&gt;
  &lt;/StatisticsCollector&gt;
  </pre>



1.  If the type attribute is not of a supported data type, then that's the cause of the error.

	

  In the example Statistics Collector policy shown above, recall that the `type` attribute is set as `char`, which is unsupported. Therefore, the deployment of the API Proxy fails with the error:

  ```
  StatisticsCollection char: Datatype Revision:1;APIProxy:StatCollector;Organization:aprabhashankar-eval;Environment:test is unsupported . Context {2}.
  ```


### Resolution

Ensure that the data type of the variables specified by the ref attributes in the `<Statistic>` elements of the Statistics Collector policy is supported and valid. For example:

<pre>
&lt;StatisticsCollector async=&quot;false&quot; continueOnError=&quot;false&quot; enabled=&quot;true&quot; name=&quot;Statistics-Collector-1&quot;&gt;
    &lt;DisplayName&gt;Statistics Collector 1&lt;/DisplayName&gt;
    &lt;Statistics&gt;
        &lt;Statistic name=&quot;statName&quot; ref=&quot;varName&quot; <var>type=&quot;string&quot;</var>&gt;defaultStatValue&lt;/Statistic&gt;
    &lt;/Statistics&gt;
&lt;/StatisticsCollector&gt;
</pre>


## InvalidName


### Error message

Deployment of the API proxy through either the Edge UI or Edge management API fails with this error message:

<pre>
Error Deploying Revision [<var>revision_number</var>]
StatisticsCollection: Name: [<var>name</var>] conflicts with system defined variables. Context policy: [<var>policy_name</var>] 
</pre>

**Example error message**

```
Error Saving Revision 1
StatisticsCollection: Name: organization conflicts with system defined variables. Context policy: StatCollector.
```

**Example screenshot**


<img alt="" src="/api-platform/images/stats-collector-deploy-error-2.png">



### Cause

If the name used to reference the data collected for the specified variable defined within the `<Statistic>` element of the Statistics Collector policy conflicts with a system-defined variable, then the deployment of the API proxy fails.

Some of the known system-defined variables are `organization` and `environment`.

For example, if the name attribute is specified as `organization` in the `<Statistic>` element of the Statistics Collector policy, then the deployment of the API proxy fails.


### Diagnosis



1.  Identify the Statistics Collector policy where the error occurred and the name of `<Statistic>` element that conflicts with a system-defined variable. You can find both these items in the error message. For example, in the following error, the policy name is `StatCollector`, the name of the `<Statistic>` element that conflicts with the system defined variable is `organization`:

  ```
  StatisticsCollection: Name: organization conflicts with system defined variables. Context policy: StatCollector.
  ```



1.  Verify that the name of the `<Statistic>` element used in the failed Statistics Collector policy XML matches the name identified in the error message (step #1 above). For example, the following policy specifies the name as `organization` in the `<Statistic>` element, which matches what's in the error message:

  <pre>
  &lt;StatisticsCollector name=&quot;StatCollector&quot;&gt;
      &lt;Statistics&gt;
          &lt;Statistic <var>name=&quot;organization&quot;</var> ref=&quot;organization&quot; type=&quot;string&quot;&gt;myorg&lt;/Statistic&gt;
          &lt;Statistic name=&quot;traffic&quot; ref=&quot;traffic&quot; type=&quot;string&quot;&gt;999999&lt;/Statistic&gt;
      &lt;/Statistics&gt;
  &lt;/StatisticsCollector&gt;
  </pre>


1.  Examine the definition of each of the `<Statistic>` elements. If there's any `<Statistic>` element where the `name` attribute conflicts with a system variable, then that is the cause of the error.

In the example Statistics Collector policy shown above, recall that the name attribute of `<Statistic>` used is `organization`, which happens to be a system variable name. Hence, the deployment of the API Proxy fails with the error:

```
StatisticsCollection: Name: organization conflicts with system defined variables. Context policy: StatCollector.
```


### Resolution

Ensure that the name of the `<Statistic>` element used to reference the data collected for the specified variable defined within the `<Statistic>` element in the Statistics Collector policy is not the same as the system-defined variables. For example:

<pre>
&lt;StatisticsCollector name=&quot;StatCollector&quot;&gt;
    &lt;Statistics&gt;
        &lt;Statistic <var>name=&quot;org&quot;</var> ref=&quot;org&quot; type=&quot;string&quot;&gt;myorg&lt;/Statistic&gt;
        &lt;Statistic name=&quot;traffic&quot; ref=&quot;traffic&quot; type=&quot;string&quot;&gt;999999&lt;/Statistic&gt;
    &lt;/Statistics&gt;
&lt;/StatisticsCollector&gt;
</pre>


## DatatypeMissing


### Error message

Deployment of the API proxy through either the Edge UI or Edge management API fails with this error message:

<pre>
Error Deploying Revision [<var>revision_number</var>]
StatisticsCollection [ref]: Datatype of Revision:[<var>revision_number</var>];APIProxy:[<var>api_proxy</var>];Organization:[<var>org_name</var>];Environment:[<var>env_name</var>] is missing . Context [<var>context</var>].
</pre>

**Example error message**

```
Error Deploying Revision 2 to test
StatisticsCollection product.id: Datatype of Revision:2;APIProxy:StatCollector;Organization:aprabhashankar-eval;Environment:test is missing. Context {2}.
```

**Example screenshot**

<img alt="" src="/api-platform/images/stats-collector-deploy-error-3.png">


### Cause

If the type of the variable specified by the `ref` attribute in the `<Statistic>` element of the Statistics Collector policy is missing, then the deployment of the API proxy fails.


### Diagnosis


1.  Identify the Statistics Collector policy in the specific API Proxy where the failure has occurred. 
1.  Examine the definition of each of the `<Statistic>` elements in the failed Statistics Collector policy XML. If there are any `<Statistic>` elements where the type attribute is missing, then that is the cause of the error.

Here's an example Statistics Collector policy:

  <pre>
  &lt;StatisticsCollector name=&quot;publishPurchaseDetails&quot;
      &lt;Statistics&gt;<var>
          &lt;Statistic name=&quot;productID&quot; ref=&quot;product.id&quot;&gt;999999&lt;/Statistic&gt;
          &lt;Statistic name=&quot;price&quot; ref=&quot;product.price&quot; type=&quot;string&quot;&gt;1000&lt;/Statistic&gt;</var>
      &lt;/Statistics&gt;
  &lt;/StatisticsCollector&gt;
  </pre>

In the example Statistics Collector policy shown above, notice that the type attribute is missing for both the `<Statistic>` elements. Therefore, the deployment of the API Proxy fails. 


### Resolution

Ensure that the type attribute of the variable specified by the `ref` attribute in the `<Statistic>` element of the Statistics Collector policy is always specified and is of supported data type.

The supported data types are `string`, `integer`, `float`, `long`, `double`, and `boolean`.

For example:

<pre>
&lt;StatisticsCollector name=&quot;publishPurchaseDetails&quot;&gt;
    &lt;Statistics&gt;
        &lt;Statistic name=&quot;productID&quot; ref=&quot;product.id&quot; <var>type=&quot;string&quot;</var>&gt;999999&lt;/Statistic&gt;
        &lt;Statistic name=&quot;price&quot; ref=&quot;product.price&quot;  <var>type=&quot;string&quot;</var>&gt;1000&lt;/Statistic&gt;
    &lt;/Statistics&gt;
&lt;/StatisticsCollector&gt;
</pre>

{% endblock %}
