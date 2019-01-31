{% extends "_base.html" %} {% block title %}XML to JSON policy deployment error troubleshooting{% endblock %} {% block body %}

<aside class="note">Was this troubleshooting playbook helpful? Please let us know
  by clicking <a href="" class="google-feedback"
   data-p="1636213" data-b="docsite" data-context="troubleshooting-feedback">
  Send Feedback 
</a>.</aside>

## EitherOptionOrFormat

### Error message

Deployment of the API proxy through either the Edge UI or Edge management API fails with this error message:

<pre class="prettyprint">
Error Saving Revision [<var>revision_number</var>]
XMLToJSON[{0}]: Either Options or Format must be specified.
</pre>

### Example error

```
Error Saving Revision 1
XMLToJSON[{0}]: Either Options or Format must be specified.
```

### Example screenshot

<img alt="" src="/api-platform/images/json2xml-error-1.png">


### Cause

If one of the elements `<Options>` or `<Format>` is not declared in the XML to JSON Policy, then the deployment of the API proxy fails.

`<Options>` is required if `<Format>` isn't used.

Use either the `<Format>` element or `<Options>` element group. You cannot use both `<Format>` and `<Options>`. Predefined formats include: `xml.com`, `yahoo`, `google`, and `badgerFish`.


### Diagnosis


1.  Examine all the XML to JSON policies in the specific API proxy where the failure has occurred. If there is any XML to JSON policy in which either of the elements `<Options>` or `<Format>` is not declared, then that's the cause of the error.

  For example, the following policy does not have either of the elements declared:

  ```
  <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
  <XMLToJSON async="false" continueOnError="false" enabled="true" name="XMLToJSON">
      <DisplayName>XMLToJSON</DisplayName>
      <Properties/>
      <OutputVariable>response</OutputVariable>
      <Source>response</Source>
  </XMLToJSON>
  ```


### Resolution

Ensure that the XML of the XML to JSON policy has one of the elements `<Options>` or `<Format>` declared in the policy. 

Example 1:

```
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<XMLToJSON async="false" continueOnError="false" enabled="true" name="XMLToJSON">
    <DisplayName>XMLToJSON</DisplayName>
    <Properties/>
    <Format>google</Format>
    <OutputVariable>response</OutputVariable>
    <Source>response</Source>
</XMLToJSON>
```

Example 2:

```
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<XMLToJSON async="false" continueOnError="false" enabled="true" name="XMLToJSON">
    <DisplayName>XMLToJSON</DisplayName>
    <Properties/>
    <Options>
        <RecognizeNumber>true</RecognizeNumber>
        <RecognizeBoolean>true</RecognizeBoolean>
        <RecognizeNull>true</RecognizeNull>
    </Options>
    <OutputVariable>response</OutputVariable>
    <Source>response</Source>
</XMLToJSON>
```


## UnknownFormat


### Error message

Deployment of the API proxy through either the Edge UI or Edge management API fails with this error message:


<pre class="prettyprint">
Error Saving Revision [<var>revision_number</var>]
XMLToJSON[<var>policy_name</var>]: Unknown Format [<var>unknown_format</var>].
</pre>


### Example error


```
Error Saving Revision 1
XMLToJSON[WithFormat]: Unknown Format google.com.
```

### Example screenshot

<img alt="" src="/api-platform/images/json2xml-error-2.png">

### Cause

If the `<Format>` element within the XML to JSON policy has an unknown format defined, then the deployment of the API proxy fails.

Predefined formats include: `xml.com`, `yahoo`, `google`, and `badgerFish`.

Note:  If you're an Apigee Edge for Private Cloud customer, you can add your own Formats to the following file on each message processor: `/opt/apigee/conf/apigee/message-processor/xmltojson-formats.xml`. Restart your message processors after modifying the file.

### Diagnosis

1.  Identify the XML to JSON policy where the error occurred and the unknown format. You can find this information from the error message. For example, in the following error, the policy name is `WithFormat` and the unknown format is `google.com`:

  ```
  Error Saving Revision 1
  XMLToJSON[WithFormat]: Unknown Format google.com.
  ```

1.  Verify that the unknown format specified in the failed XML to JSON policy matches with the value identified in the error message (step #1 above). For example, the following policy specifies the format as `google.com`, which matches what's in the error message:

  ```
  <XMLToJSON async="false" continueOnError="false" enabled="true" name="WithFormat">
    <DisplayName>WithFormat</DisplayName>
    <Properties/>
    <Format>google.com</Format>
    <OutputVariable>response</OutputVariable>
    <Source>response</Source>
  </XMLToJSON>
  ```

1.  If the Format specified is not one among the predefined formats `xml.com`, `yahoo`, `google`, or `badgerFish`, then that's the cause of the error.

    In the example XML to JSON Policy shown above, the format is `google.com` which is invalid. Therefore, the deployment of the API Proxy fails with the error: 

  ```
  XMLToJSON[WithFormat]: Unknown Format google.com.
  ```

### Resolution

Ensure that the format specified in the `<Format>` element of the XML to JSON policy
  is valid. For example:

```
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<XMLToJSON async="false" continueOnError="false" enabled="true" name="WithFormat">
  <DisplayName>WithFormat</DisplayName>
  <Properties/>
  <Format>google</Format>
  <OutputVariable>response</OutputVariable>
  <Source>response</Source>
</XMLToJSON>
```

{% endblock %}
