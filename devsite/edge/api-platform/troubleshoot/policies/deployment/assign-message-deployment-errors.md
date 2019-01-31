{% extends "_base.html" %} {% block title %}Assign Message policy deployment error troubleshooting{% endblock %} {% block body %}

<aside class="note">Was this troubleshooting playbook helpful? Please let us know
  by clicking <a href="" class="google-feedback"
   data-p="1636213" data-b="docsite" data-context="troubleshooting-feedback">
  Send Feedback 
</a>.</aside>


## InvalidIndex


### Error message

Deployment of the API proxy through either the Edge UI or Edge management API fails with this error message:

<pre>
Error in deployment for environment [<var>environment</var>]
The revision is deployed, but traffic cannot flow. 
AssignMessage[<var>policy_name</var>]: index must be greater than zero in [<var>attribute</var>].[<var>index</var>]
</pre>

**Example error message**

```
Error in deployment for environment test.
The revision is deployed, but traffic cannot flow.
AssignMessage[GenerateStudentsRequest]: index must be greater than zero in id.0
```

**Example screenshot**

<img alt="" src="/api-platform/images/am-deploy-error-1.png">


### Cause

If the index specified in the `<Copy>` and/or `<Remove>` elements of the Assign Message policy is 0 or a negative number, then deployment of the API Proxy fails. 

For example, if you pass multiple query parameters with the same name, you can access them in Edge as indexed flow variables.  Let's say you want to pass the IDs of 3 students as query parameters, then you can do it as follows:

```
https://myorg-test.apigee.net/v1/basepath?school_name=NPS&id=1&id=2&id=3
```

Then, let's say you attempt to access these query parameters with index numbers 0, 1, and 2 in the Assign Message policy as follows:

```
id.0
id.1 and
id.2
```

In this case, the deployment of the proxy fails because the index must start with 1.  This means, you can access the first query parameter "id=1" as "id.1".  To access the second query parameter "id=2", you need to use the index 2 i.e, "id.2".  Similarly, to access the third query parameter "id=3", you can use "id.3". 

You can find more information [here](/api-platform/reference/policies/assign-message-policy#copyelement-copyqueryparamselement).


### Diagnosis


1.  Identify the Assign Message policy where the error occurred, the name of the attribute, and the invalid index. You can find all these items in the error message. For example, in the following error, the policy name is `GeneratingGeocodingRequest`, the name of the attribute is `id`, and the index is `0`:

  ```
  AssignMessage[GenerateStudentsRequest]: index must be greater than zero in id.0
  ```



1.  Verify that the attribute name and the index used in the failed Assign Message policy XML matches the attribute name and the index identified in the error message (step #1 above). For example, the following policy specifies the attribute `id` and index as `0`, which matches what's in the error message:

  <pre class="prettyprint">
  &lt;AssignMessage name=&quot;GenerateStudentsRequest&quot;&gt;
      &lt;AssignTo createNew=&quot;true&quot; type=&quot;request&quot;&gt;StudentsInfoRequest&lt;/AssignTo&gt;
      &lt;Copy source=&quot;request&quot;&gt;
          &lt;QueryParams&gt;
              &lt;QueryParam name=&quot;school_name&quot;/&gt;
              <var>&lt;QueryParam name=&quot;id.0&quot;/&gt;</var>
              &lt;QueryParam name=&quot;id.1&quot;/&gt;
              &lt;QueryParam name=&quot;id.2&quot;/&gt;
          &lt;/QueryParams&gt;
      &lt;/Copy&gt;
  &lt;/AssignMessage&gt;
  </pre>



1.  If the index specified is 0 or negative number, then that's the cause of the error.

  In the example Assign Message policy shown above, you are trying to access the first value of the query parameter "id" using the index 0. Hence the deployment of the API Proxy fails with the error:

  ```
  AssignMessage[GenerateStudentsRequest]: index must be greater than zero in id.0
  ```


### Resolution

In Assign Message, when you access multiple headers, query parameters, or other parameters that share the same name, ensure that the index is always greater than zero.  For example:

```
<AssignMessage name="GenerateStudentsRequest">
    <AssignTo createNew="true" type="request">StudentsInfoRequest</AssignTo>
    <Copy source="request">
        <QueryParams>
            <QueryParam name="school_name"/>
            <QueryParam name="id.1"/>
            <QueryParam name="id.2"/>
            <QueryParam name="id.3"/>
        </QueryParams>
    </Copy>
</AssignMessage>
```


## InvalidVariableName


### Error message

Deployment of the API proxy through either the Edge UI or Edge management API fails with this error message:

<pre>
Error Saving Revision [<var>revision_number</var>]
The revision is deployed, but traffic cannot flow. AssignMessage schema validation failed: invalid variable name - null - in assign variable.
</pre>

**Example error message**

```
Error Saving Revision 3
AssignMessage schema validation failed: invalid variable name - null - in assign variable.
```

**Example screenshot**


<img alt="" src="/api-platform/images/am-deploy-error-2.png">


### Cause

If the child element `<Name>` is empty or not specified in the `<AssignVariable>` element, then the deployment of the API proxy fails because there is no valid variable name to which to assign a value. A valid variable name is required. 


### Diagnosis

Examine all the Assign Message policies in the specific API Proxy where the failure has occurred. If there is any Assign Message policy in which the child element `<Name>` is empty or not specified in the `<AssignVariable>` element, then that's the cause of the error.

**Example 1: The following Assign Message policy has no `<Name>` element defined.** 

```
<AssignMessage async="false" continueOnError="false" enabled="true" name="Assign-Message-1">
    <DisplayName>Assign Message-1</DisplayName>
    <Properties/>
    <AssignVariable>
        <Value>abcd1234</Value>
    </AssignVariable>
    <IgnoreUnresolvedVariables>false</IgnoreUnresolvedVariables>
    <AssignTo createNew="true" transport="http" type="request">googleBookReq</AssignTo>
</AssignMessage>
```

**Example 2: The following Assign Message policy has an empty `<Name>` element .**

```
<AssignMessage async="false" continueOnError="false" enabled="true" name="Assign-Message-1">
    <DisplayName>Assign Message-1</DisplayName>
    <Properties/>
    <AssignVariable>
        <Name></Name>         
        <Value>abcd1234</Value>
    </AssignVariable>
    <IgnoreUnresolvedVariables>false</IgnoreUnresolvedVariables>
    <AssignTo createNew="true" transport="http" type="request">googleBookReq</AssignTo>
</AssignMessage>
```

### Resolution

Ensure that the child element `<Name>` is always specified and non-empty within the `<AssignVariable>` element of the Assign Message policy. For example: 

<pre class="prettyprint">
&lt;AssignMessage async=&quot;false&quot; continueOnError=&quot;false&quot; enabled=&quot;true&quot; name=&quot;Assign-Message-1&quot;&gt;
    &lt;DisplayName&gt;Assign Message-1&lt;/DisplayName&gt;
    &lt;Properties/&gt;
        &lt;AssignVariable&gt;
        <var>&lt;Name&gt;appSecret&lt;/Name&gt;</var>
        &lt;Value&gt;abcd1234&lt;/Value&gt;
    &lt;/AssignVariable&gt;
    &lt;IgnoreUnresolvedVariables&gt;false&lt;/IgnoreUnresolvedVariables&gt;
    &lt;AssignTo createNew=&quot;true&quot; transport=&quot;http&quot; type=&quot;request&quot;&gt;googleBookReq&lt;/AssignTo&gt;
&lt;/AssignMessage&gt;
</pre>

{% endblock %}

