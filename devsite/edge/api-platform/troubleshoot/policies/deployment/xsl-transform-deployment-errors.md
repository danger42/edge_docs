
{% extends "_base.html" %} {% block title %}XSL Transform policy deployment error troubleshooting{% endblock %} {% block body %}

<aside class="note">Was this troubleshooting playbook helpful? Please let us know
  by clicking <a href="" class="google-feedback"
   data-p="1636213" data-b="docsite" data-context="troubleshooting-feedback">
  Send Feedback 
</a>.</aside>

## XSLEmptyResourceUrl


### Error message

Deployment of the API proxy through either the Edge UI or Edge management API
fails with this error message:


<pre class="prettyprint">
Error Saving Revision <var>revision_number</var>
Error occurred while validation of bean <var>policy_name</var>.xml. Reason:- Non null
value expected for element ResourceURL in XSL
</pre>


### Example error message

In the following example error message, the name of the XSL Transform policy causing
the error is `xslt`:

```
Error Saving Revision 1
Error occurred while validation of bean xslt.xml. Reason: - Non null value
expected for element ResourceURL in XSL
```

### Example screenshot

In the Edge UI, you will see a pop-up error similar to the following:

<img alt="" src="/api-platform/images/xsl-transform-error-1.png">


### Cause

If the `<ResourceURL>` element in the XSL Transform policy is empty, then the
deployment of the API proxy fails.


### Diagnosis

Examine the `<ResourceURL>` element in the XSL Transform policy named in the error
message. If there is no Resource URL specified in the `<ResourceURL>` element,
then that is the cause of the error. For example, the following XSL Transform
policy has an empty `<ResourceURL>` element:

```
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<XSL async="false" continueOnError="false" enabled="true" name="xslt">
    <DisplayName>xslt</DisplayName>
    <Properties/>
    <ResourceURL></ResourceURL>
    <Parameters ignoreUnresolvedVariables="true"/>
    <OutputVariable/>
</XSL>
```

Because the `<ResourceURL>` element is empty, the deployment of the API proxy fails.


### Resolution

Ensure that the `<ResourceURL>` element in the XSL Transform policy has a valid
URL pointing to an XSLT file.

For example:

```
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<XSL async="false" continueOnError="false" enabled="true" name="xslt">
    <DisplayName>xslt</DisplayName>
    <Properties/>
    <ResourceURL>xsl://my_transform.xsl</ResourceURL>
    <Parameters ignoreUnresolvedVariables="true"/>
    <OutputVariable/>
</XSL>
```


## XSLInvalidResourceType


### Error message

Deployment of the API proxy through either the Edge UI or Edge management API
fails with this error message:

<pre class="prettyprint">
Error Deploying Revision <var>revision_number</var> to <var>env_name</var>
XSL <var>policy_name</var>: Resource type must be xsl. Context Revision:<var>revision_number</var>;
APIProxy:<var>api_proxy_name</var>;Organization:<var>org_name</var>;Environment:<var>env_name</var>.
</pre>


### Example error message

In the following example error message, the name of the XSL Transform policy
causing the error is `xslt`:

```
Error Deploying Revision 1 to test
XSL xslt: Resource type must be xsl. Context Revision:1;APIProxy:XSLTransform;
Organization:jdoe-test;Environment:test.
```

### Example screenshot

In the Edge UI, you will see a pop-up error similar to the following:

<img alt="" src="/api-platform/images/xsl-transform-error-2.png">


### Cause

If the resource type specified in the `<ResourceURL>` element of the XSL Transform
policy is not of type `xsl`, then the deployment of the API proxy fails.

The correct format to specify this is as shown below:

```
<ResourceURL>xsl://<file_name>.xsl</ResourceURL>
```

For example, if the resource type is specified as `jsc` in the `<ResourceURL>`
element of the XSL Transform policy as shown below, then the deployment of the
API proxy fails:

```
<ResourceURL>jsc://my_transform.xsl</ResourceURL>
```

### Diagnosis

1.  Identify the name of the XSL Transform policy where the error has occurred. You can find this information from the error message. For example, in the following error, the policy name is `xslt`.

  ```
  XSL xslt: Resource type must be xsl. Context Revision:1;APIProxy:XSLTransform;
  Organization:jdoe-test;Environment:test.
  ```

1.  In the failed XSL Transform policy XML, verify if the type of the resource specified in the `<ResourceURL>` element is not of type `xsl`. If it is not of `xsl` type, then that's the cause of the error.

  For example, the following policy specifies a non `xsl` type in the `<ResourceURL>` element:

  ```
  <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
  <XSL async="false" continueOnError="false" enabled="true" name="xslt">
      <DisplayName>xslt</DisplayName>
      <Properties/>
      <ResourceURL>jsc://my_transform.xsl</ResourceURL>
      <Parameters ignoreUnresolvedVariables="true"/>
      <OutputVariable/>
  </XSL>
  ```

  Because the resource URL is specified as `jsc://my_transform.xsl`, which is notof `xsl` type, the deployment of the API Proxy fails with the error:

  ```
  XSL xslt: Resource type must be xsl. Context Revision:1;APIProxy:XSLTransform;
  Organization:jdoe-test;Environment:test.
  ```

### Resolution

Ensure that the resource type specified in the `<ResourceURL>` element of the XSL
Transform policy is always of `xsl` type. For example:

  ```
  <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
  <XSL async="false" continueOnError="false" enabled="true" name="xslt">
      <DisplayName>xslt</DisplayName>
      <Properties/>
      <ResourceURL>xsl://my_transform.xsl</ResourceURL>
      <Parameters ignoreUnresolvedVariables="true"/>
      <OutputVariable/>
  </XSL>
  ```

{% endblock %}
