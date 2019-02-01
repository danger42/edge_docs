{% extends "_base.html" %} {% block title %}JSON to XML policy runtime error troubleshooting{% endblock %} {% block body %}

<aside class="note">Was this troubleshooting playbook helpful? Please let us know
  by clicking <a href="" class="google-feedback"
   data-p="1636213" data-b="docsite" data-context="troubleshooting-feedback">
  Send Feedback 
</a>.</aside>

## SourceUnavailable


### Error code


```
steps.json2xml.SourceUnavailable
```



### Error response body


<pre class="prettyprint">
{
    "fault": {
        "faultstring": "JSONToXML[<var>policy_name</var>]: Source [<var>source_variable</var>] is not available",
        "detail": {
            "errorcode": "steps.json2xml.SourceUnavailable"
        }
    }
}
</pre>


### Example error


```
{
    "fault": {
        "faultstring": "JSONToXML[Convert-JSONToXML]: Source response is not available",
        "detail": {
            "errorcode": "steps.json2xml.SourceUnavailable"
        }
    }
}
```


### Cause

This error occurs if the [message](https://docs.apigee.com/api-platform/reference/variables-reference#messagevariables) variable specified in the `<Source>` element of the JSON to XML policy is either:

*   Out of scope (not available in the specific flow where the policy is being executed) or
*   Can't be resolved (is not defined)

For example, this error occurs if the JSON to XML policy is supposed to be executed in the request flow, but the `<Source>` element is set to the `response` variable, which doesn't exist in the request flow.


### Diagnosis



1.  Identify the JSON to XML policy where the error occurred and the name of the variable that is not available. You can find both of these items in the `faultstring` element of the error response. For example, in the following `faultstring`, the policy name is `Convert-JSONToXML` and the variable is `response`:

    ```
    "faultstring": "JSONToXML[Convert-JSONToXML]: Source response is not available"
    ```

1.  In the failed JSON to XML policy XML, verify that the name of the variable set in the `<Source>` element matches the variable name identified in the fault string (step #1 above). For example, the following JSON to XML policy specifies a variable named `response` in the `<Source>` element, which matches what's in the `faultstring`:

    ```
    <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
    <JSONToXML async="false" continueOnError="false" enabled="true" name="Convert-JSONToXML">
        <DisplayName>Convert-JSONToXML</DisplayName>
        <Properties/>
        <Options>
            <NullValue>NULL</NullValue>
            <NamespaceBlockName>#namespaces</NamespaceBlockName>
        <DefaultNamespaceNodeName>$default</DefaultNamespaceNodeName>
            <NamespaceSeparator>:</NamespaceSeparator>
            <TextNodeName>#text</TextNodeName>
            <AttributeBlockName>#attrs</AttributeBlockName>
            <AttributePrefix>@</AttributePrefix>
            <InvalidCharsReplacement>_</InvalidCharsReplacement>
            <ObjectRootElementName>Root</ObjectRootElementName>
            <ArrayRootElementName>Array</ArrayRootElementName>
            <ArrayItemElementName>Item</ArrayItemElementName>
        </Options>
        <OutputVariable>request</OutputVariable>
        <Source>response</Source>
    </JSONToXML>
    ```


1.  Determine if the variable used in the `<Source>` element is defined and available in the flow in which the JSON to XML policy is being executed.
1.  If the variable is either:
    *   Out of scope (not available in the specific flow where the policy is being executed) or
    *   Can't be resolved (is not defined)

    then that's the cause for the error.


    As an example, let's say the JSON to XML policy shown above is supposed to be executed in the **request** flow.  Recall that the `response` variable is used in the `<Source>` element of the JSON to XML policy.  The response variable is available only in the response flow. 


    Because the `response` variable does not exist in the request flow, you receive the error code:


    ```
    steps.json2xml.SourceUnavailable
    ```

### Resolution

Ensure that the variable set in the `<Source>` element of the failed JSON to XML policy is defined and exists in the flow where the policy executes.

To correct the example JSON to XML policy shown above, you could modify the `<Source>` element to use the `request` variable, because it exists in the request flow:

  ```
  <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
  <JSONToXML async="false" continueOnError="false" enabled="true" name="Convert-JSONToXML">
      <DisplayName>Convert-JSONToXML</DisplayName>
      <Properties/>
      <Options>
          <NullValue>NULL</NullValue>
          <NamespaceBlockName>#namespaces</NamespaceBlockName>
          <DefaultNamespaceNodeName>$default</DefaultNamespaceNodeName>
          <NamespaceSeparator>:</NamespaceSeparator>
          <TextNodeName>#text</TextNodeName>
          <AttributeBlockName>#attrs</AttributeBlockName>
          <AttributePrefix>@</AttributePrefix>
          <InvalidCharsReplacement>_</InvalidCharsReplacement>
          <ObjectRootElementName>Root</ObjectRootElementName>
          <ArrayRootElementName>Array</ArrayRootElementName>
          <ArrayItemElementName>Item</ArrayItemElementName>
      </Options>
      <OutputVariable>request</OutputVariable>
      <Source>request</Source>
  </JSONToXML>
  ```

## ExecutionFailed


### Error code


```
steps.json2xml.ExecutionFailed
```


### Error response body


<pre class="prettyprint">
{
   "fault": {
        "faultstring": "JSONToXML[<var>policy_name</var>]: Execution failed due to reason: Expecting { or [ at line 1",
        "detail": {
            "errorcode": "steps.json2xml.ExecutionFailed"
        }
    }
}
</pre>



### Possible causes

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

<a href="#cause_missing_input_payload">Missing input payload</a>
   </td>
   <td>The input payload (JSON) is empty.
   </td>
  </tr>
  <tr>
   <td>

<a href="#cause_invalid_or_malformed_input">Invalid or Malformed Input</a>
   </td>
   <td>The input (JSON) passed to JSON to XML policy is invalid or malformed.
   </td>
  </tr>
</table>



### Cause: Missing input payload

In the the JSON to XML policy if the content (payload) of the variable specified in the `<Source>` element is empty, then this error occurs.

For example, If the `<Source>` element in the JSON to XML policy is set as a `request` or `response` variable and they are supposed to contain a JSON payload, but if the payload is empty, then the error occurs.


### Diagnosis



1.  Identify the JSON to XML policy where the error occurred. You can find this information in the `faultstring` element of the error response. For example, in the following `faultstring`, the policy name is `Convert-JSONToXML`:

    ```
    "faultstring": "JSONToXML[Convert-JSONToXML]: Execution failed due to reason: Expecting { or [ at line 1"
    ```


1.  Examine the `<Source>` element in the failed JSON to XML policy XML and determine the variable specified. For example, the following JSON to XML policy has the `<Source>` element set to `request`:


  ```
  <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
  <JSONToXML async="false" continueOnError="false" enabled="true" name="Convert-JSONToXML">
  <DisplayName>Convert-JSONToXML</DisplayName>
  <Properties/>
  <Options>
      <NullValue>NULL</NullValue>
      <NamespaceBlockName>#namespaces</NamespaceBlockName>        
      <DefaultNamespaceNodeName>$default</DefaultNamespaceNodeName>
      <NamespaceSeparator>:</NamespaceSeparator>
      <TextNodeName>#text</TextNodeName>
      <AttributeBlockName>#attrs</AttributeBlockName>
      <AttributePrefix>@</AttributePrefix>
      <InvalidCharsReplacement>_</InvalidCharsReplacement>
      <ObjectRootElementName>Root</ObjectRootElementName>
      <ArrayRootElementName>Array</ArrayRootElementName>
      <ArrayItemElementName>Item</ArrayItemElementName>
  </Options>
  <OutputVariable>request</OutputVariable>
  <Source>request</Source>
  </JSONToXML>
  ```

1.  Check if the variable specified for the `<Source>` element in the policy is empty.  If it is empty, then that's the cause for the error.

    In the example JSON to XML policy shown above, the request payload (that is, the request body) that was sent by the client is empty.


    For example:


    ```
    curl -v "http://<org>-<env>.apigee.net/v1/testjsontoxml" -H "Content-Type: application/json"
    ```

    Because the JSON response payload is empty, you receive the error code:

  ```
  steps.json2xml.ExecutionFailed
  ```

   This error can also occur if the `<Source>` element is set to response, but an empty payload is passed by the backend server. 


   Note: The HTTP Content-Type header of the source message must be set to **application/json**; otherwise, the policy is skipped during processing and is therefore not enforced.

### Resolution

Ensure that the input passed to the JSON to XML policy in the `<Source>` element is valid JSON payload and non empty.

To fix the issue with the sample JSON to XML policy, pass a valid JSON payload. For example:


1.  Create a file named `city.json` with the following contents:

    ```
    { 
      "Name":"Apigee",
      "City":"Bengaluru",
      "Pincode":"560016"
    }
    ```

1.  Make the API call using a cURL command as follows:

    ```
    curl -v "http://<org>-<env>.apigee.net/v1/testxmltojson" -H "Content-Type: application/json" -X POST -d @company.json
    ```

### Cause: Invalid or Malformed Input

If the JSON to XML policy parses input that is invalid or malformed, then you get this error.

For example, if the following invalid JSON is provided as input to JSON to XML policy, 

```
[
    "args": ["name" : "Google" ]
]
```

you will get the error:


```
"faultstring": "JSONToXML[Convert-JSONToXML]: Execution failed due to reason: Expecting { or [ at line 1"
```

### Diagnosis

1.  Identify the JSON to XML policy where the error occurred. You can find this information in the `faultstring` element of the error response. For example, in the following `faultstring`, the policy name is `Convert-JSONToXML`:

  ```
  "faultstring": "JSONToXML[Convert-JSONToXML]: Execution failed due to reason: Expecting { or [ at line 1"
  ```

1.  Examine the `<Source>` element specified in the failed JSON to XML policy XML. For example, the following JSON to XML policy has the `<Source>` element set to `request` variable:

  ```
  <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
  <JSONToXML async="false" continueOnError="false" enabled="true" name="Convert-JSONToXML">
    <DisplayName>Convert-JSONToXML</DisplayName>
    <Properties/>
    <Options>
        <NullValue>NULL</NullValue>
        <NamespaceBlockName>#namespaces</NamespaceBlockName>
        <DefaultNamespaceNodeName>$default</DefaultNamespaceNodeName>
        <NamespaceSeparator>:</NamespaceSeparator>
        <TextNodeName>#text</TextNodeName>
        <AttributeBlockName>#attrs</AttributeBlockName>
        <AttributePrefix>@</AttributePrefix>
        <InvalidCharsReplacement>_</InvalidCharsReplacement>
        <ObjectRootElementName>Root</ObjectRootElementName>
        <ArrayRootElementName>Array</ArrayRootElementName>
        <ArrayItemElementName>Item</ArrayItemElementName>
    </Options>
    <OutputVariable>request</OutputVariable>
    <Source>request</Source>
  </JSONToXML>
  ```


1.  Validate if the input specified in the `<Source>` element is a valid JSON payload.
If the input is invalid or malformed, then that's the cause for the error.

   Assume that the following invalid JSON was passed to the policy

    ```
    [
        "args": ["name" : "Google" ]
    ]
    ```

    Here's the sample API call that shows how the request was passed:

    ```
    curl -v "http://<org>-<env>.apigee.net/v1/testjsontoxml" -H "Content-Type:
    application/json" -X POST -d '[ "args" : ["name" : "Google" ]]'
    ```

    The JSON payload passed in the request is invalid because the JSON object starts
    and ends with square brackets ([ ]). For this reason, you receive the error code:


  `steps.json2xml.ExecutionFailed`


   Note: A JSON payload can contain JSON objects and/or JSON arrays. A valid JSON object starts with { and ends with }. A valid JSON array (on the outermost level), that starts with [ and ends with ].  


   This error can also occur if the `<Source>` element was set to response but the JSON response payload is invalid or malformed.


   Note: The HTTP Content-type header of the source message must be set to **application/json**, otherwise the policy is not enforced, meaning it is skipped during processing.


### **Resolution**

Ensure that the input passed to JSON to XML policy in the `<Source>` element is valid and not malformed.

To fix the issue with the sample JSON to XML policy discussed above, pass a valid JSON payload request as follows:

```
{  
  "args"  : {  

        "name"  :  "Google"
   }
}
```


## OutputVariableIsNotAvailable


### Error code


```
steps.json2xml.OutputVariableIsNotAvailable
```



### Error response body


<pre class="prettyprint">
{
    "fault": {
        "faultstring": "JSONToXML[<var>policy_name</var>]: Output variable is not available.",
        "detail": {
            "errorcode": "steps.json2xml.OutputVariableIsNotAvailable"
        }
    }
}
</pre>


### Example error


```
{
    "fault": {
        "faultstring": "JSONToXML[Check-JSONToXML]: Output variable is not available.",
        "detail": {
            "errorcode": "steps.json2xml.OutputVariableIsNotAvailable"
        }
    }
}
```

### Cause

This error occurs if the variable specified in the `<Source>` element of the JSON to XML Policy is of type string and the `<OutputVariable>` element is not defined. The `<OutputVariable>` element is mandatory when the variable defined in the `<Source>` element is of type string. 

Note: The `<OutputVariable>` is not mandatory if the `<Source>` element is of type message. 


### Diagnosis

1.  Identify the JSON to XML Policy where the error has occurred. You can find this in the `faultstring` element of the error response. For example, in the following `faultstring`, the policy name is `Check-JSONToXML`:

    ```
    "faultstring": "JSONToXML[Check-JSONToXML]: Output variable is not available."
    ```

1.  In the failed JSON to XML Policy validate if the `<OutputVariable>` is missing. 

  The following sample JSONToXML policy has a missing  `<OutputVariable>` element:

  ```
  <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
  <JSONToXML async="false" continueOnError="false" enabled="true" name="JSONToXML">
      <DisplayName>Check-JSONToXML</DisplayName>
      <Properties/>
      <Options>
          <NullValue>NULL</NullValue>
          <NamespaceBlockName>#namespaces</NamespaceBlockName>
          <DefaultNamespaceNodeName>$default</DefaultNamespaceNodeName>
          <NamespaceSeparator>:</NamespaceSeparator>
          <TextNodeName>#text</TextNodeName>
          <AttributeBlockName>#attrs</AttributeBlockName>
          <AttributePrefix>@</AttributePrefix>
          <InvalidCharsReplacement>_</InvalidCharsReplacement>
          <ObjectRootElementName>Root</ObjectRootElementName>
          <ArrayRootElementName>Array</ArrayRootElementName>
          <ArrayItemElementName>Item</ArrayItemElementName>
      </Options>
      <Source>PostalCode</Source>
  </JSONToXML>
  ```

1.  Determine the type of variable specified in `<Source>` element:
    1.  Locate the code within the API Proxy where the variable was defined first.
    1.  Once you figure out the policy in which the variable is defined and populated first, you need to determine the type of that variable as follows:
        1.  Check the value of the type attribute (if present).
        1.  If the type attribute is not present, then the variable is considered to be a string.
    1.  If the variable's type is string, then that's the cause of the error. You can learn about common variables and their types in the [Variables reference](https://docs.apigee.com/api-platform/reference/variables-reference).

    For example, look at the `PostalCode` variable in the above JSON to XML policy.

    As an example, consider that an Assign Message policy is used to assign a value to a  variable called `PostalCode` as shown below:

    ```
    <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
    <AssignMessage async="false" continueOnError="false" enabled="true" name="Assign_PostalCode">
        <DisplayName>Assign_PostalCode</DisplayName>
        <Properties/>
        <AssignVariable>
            <Name>PostalCode</Name>
            <Value>{"value":"56008"}</Value>
            <Ref/>
        </AssignVariable>
        <IgnoreUnresolvedVariables>true</IgnoreUnresolvedVariables>
        <AssignTo createNew="false" transport="http" type="request"/>
    </AssignMessage>
    ```

    Note that the variable type set in `<AssignVariable>` is string. So the variable `PostalCode` is of type string.

    Now, recall that the `PostalCode` variable is used in the `<Source>` element of the JSONToXML policy:

    ```
    <Source>PostalCode</Source>
    ```

    Because `PostalCode` is of string type and the `<OutputVariable>` element is missing, you receive the error code:

    ```
    steps.json2xml.OutputVariableIsNotAvailable
    ```

### Resolution

Ensure that if the variable specified in the `<Source>` element of the JSONToXML Policy is of type string, the `<OutputVariable>` element is defined within the policy.

In order to correct the JSONToXML Policy discussed above, include an `<OutputVariable>` element as shown below.

  ```
  <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
  <JSONToXML async="false" continueOnError="false" enabled="true" name="JSONToXML">
      <DisplayName>Check-JSONToXML</DisplayName>
      <Properties/>
      <Options>
          <NullValue>NULL</NullValue>
          <NamespaceBlockName>#namespaces</NamespaceBlockName>
          <DefaultNamespaceNodeName>$default</DefaultNamespaceNodeName>
          <NamespaceSeparator>:</NamespaceSeparator>
          <TextNodeName>#text</TextNodeName>
          <AttributeBlockName>#attrs</AttributeBlockName>
          <AttributePrefix>@</AttributePrefix>
          <InvalidCharsReplacement>_</InvalidCharsReplacement>
          <ObjectRootElementName>Root</ObjectRootElementName>
          <ArrayRootElementName>Array</ArrayRootElementName>
          <ArrayItemElementName>Item</ArrayItemElementName>
      </Options>
      <OutputVariable>response</OutputVariable>
      <Source>PostalCode</Source>
  </JSONToXML>
  ```

## InCompatibleTypes


### Error code

```
steps.json2xml.InCompatibleTypes
```

### Error response body


<pre class="prettyprint">
{
    "fault": {
        "faultstring": "JSONToXML[<var>policy_name</var>]: String can not be assigned to message type.",
        "detail": {
            "errorcode": "steps.json2xml.InCompatibleTypes"
        }
    }
}
</pre>


### Example error


```
{
    "fault": {
        "faultstring": "JSONToXML[JSONToXML_checktype]: String can not be assigned to message type.",
        "detail": {
            "errorcode": "steps.json2xml.InCompatibleTypes"
        }
    }
}
```


### Cause

This error occurs if the type of the variable defined in the `<Source>` element and the `<OutputVariable>` element are not the same. It is mandatory that the type of the variables contained within the `<Source>` element and the `<OutputVariable>` element matches. 

The valid types are `message` and `string`.


### Diagnosis


1.  Identify the JSON to XML Policy where the error has occurred. You can find this in the `faultstring` element of the error response. For example, in the following `faultstring`, the policy name is `JSONToXML_checktype`:

    ```
    "faultstring": "JSONToXML[JSONToXML_checktype]: String can not be assigned to message type."
    ```

1.  In the failed JSON to XML Policy note the values specified in `<OutputVariable>` and `<Source>`.

  Consider the following example policy:


  ```
  <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
  <JSONToXML async="false" continueOnError="false" enabled="true" name="JSONToXML_checktype">
      <DisplayName>JSONToXML_checktype</DisplayName>
      <Properties/>
      <Options>
          <NullValue>NULL</NullValue>
          <NamespaceBlockName>#namespaces</NamespaceBlockName>
          <DefaultNamespaceNodeName>$default</DefaultNamespaceNodeName>
          <NamespaceSeparator>:</NamespaceSeparator>
          <TextNodeName>#text</TextNodeName>
          <AttributeBlockName>#attrs</AttributeBlockName>
          <AttributePrefix>@</AttributePrefix>
          <InvalidCharsReplacement>_</InvalidCharsReplacement>
          <ObjectRootElementName>Root</ObjectRootElementName>
          <ArrayRootElementName>Array</ArrayRootElementName>
          <ArrayItemElementName>Item</ArrayItemElementName>
      </Options>
      <OutputVariable>response</OutputVariable>
      <Source>PostalCode</Source>
  </JSONToXML>

  ```


1.  Determine the type of variable specified in `<Source>` and `<OutputVariable>` elements:
    1.  Locate the code within the API Proxy where each of these variable were defined first.
    1.  Once you figure out the policy in which the variable is defined and populated first, you need to determine the type of that variable as follows:
        1.  Check the value of the type attribute (if present).
        1.  If the type attribute is not present, then the variable is considered to be a string.
    1.  If the type of the variable specified in `<Source>` is string while the type of `<OutputVariable>` is message or vice versa, then that's the cause of the error. You can learn about common variables and their types in the [Variables reference](https://docs.apigee.com/api-platform/reference/variables-reference).

    As an example, consider an Assign Message policy that is used to assign a value to a  variable called `PostalCode` as shown below:


    ```
    <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
    <AssignMessage async="false" continueOnError="false" enabled="true" name="Assign_PostalCode">
        <DisplayName>Assign_PostalCode</DisplayName>
        <Properties/>
        <AssignVariable>
            <Name>PostalCode</Name>
            <Value>{"value":"56008"}</Value>
            <Ref/>
        </AssignVariable>
        <IgnoreUnresolvedVariables>true</IgnoreUnresolvedVariables>
        <AssignTo createNew="false" transport="http" type="request"/>
    </AssignMessage>
    ```


    Note that the variable type set in `<AssignVariable>` is string; therefore, the variable `PostalCode` is of type string.

    Now, recall that the `PostalCode` variable is used in the `<Source>` element of the JSONToXML policy:

    ```
    <Source>PostalCode</Source>
    ```

    Similarly, recall that the `response` variable is used in the `<OutputVariable>` element of the JSONToXML policy:

    ```
    <OutputVariable>response</OutputVariable>
    ```

    Because `PostalCode` is of string type while `response` variable is of message type, they are incompatible types so you receive the error code:

  ```
  steps.json2xml.InCompatibleTypes
  ```

    The above error can also occur if the variable in the `<Source>` element is of type `message` but the variable in the `<OutputVariable>` element is of type string.


### Resolution

Ensure that the type of the variable defined in the `<Source>` element and the `<OutputVariable>` element are always the same. It is mandatory that the type of the variables contained within the `<Source>` element and the `<OutputVariable>` element matches. That is, ensure that the type of `<Source>` and the `<OutputVariable>` elements are both string or message type.

To correct the JSON to XML Policy discussed above, you could declare another variable `PostalCode_output` of type string using the Assign Message Policy, and use this variable in the `<OutputVariable>` element of the JSON to XML Policy.

Modified Assign Message policy:

```
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<AssignMessage async="false" continueOnError="false" enabled="true" name="Assign_PostalCode">
    <DisplayName>Assign_PostalCode</DisplayName>
    <Properties/>
    <AssignVariable>
        <Name>PostalCode</Name>
        <Value>{"value":"56008"}</Value>
        <Ref/>
    </AssignVariable>
    <AssignVariable>
        <Name>PostalCode_output</Name>
        <Ref/>
    </AssignVariable>
    <IgnoreUnresolvedVariables>true</IgnoreUnresolvedVariables>
    <AssignTo createNew="false" transport="http" type="request"/>
</AssignMessage>
```

Modified JSONToXML policy:


```
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<JSONToXML async="false" continueOnError="false" enabled="true" name="JSONToXML">
    <DisplayName>JSONToXML_checktype</DisplayName>
    <Properties/>
    <Options>
        <NullValue>NULL</NullValue>
        <NamespaceBlockName>#namespaces</NamespaceBlockName>
        <DefaultNamespaceNodeName>$default</DefaultNamespaceNodeName>
        <NamespaceSeparator>:</NamespaceSeparator>
        <TextNodeName>#text</TextNodeName>
        <AttributeBlockName>#attrs</AttributeBlockName>
        <AttributePrefix>@</AttributePrefix>
        <InvalidCharsReplacement>_</InvalidCharsReplacement>
        <ObjectRootElementName>Root</ObjectRootElementName>
        <ArrayRootElementName>Array</ArrayRootElementName>
        <ArrayItemElementName>Item</ArrayItemElementName>
    </Options>
    <OutputVariable>PostalCode_output</OutputVariable>
    <Source>PostalCode</Source>
</JSONToXML>
```


## InvalidSourceType


### Error code

```
steps.json2xml.InvalidSourceType
```

### Error response body

<pre class="prettyprint">
{
    "fault": {
        "faultstring": "JSONToXML[class <var>invalid_class</var>]: Invalid source type class <var>invalid_class</var>. Valid source types are [message, string].",
        "detail": {
            "errorcode": "steps.json2xml.InvalidSourceType"
        }
    }
}
</pre>

### Example error


```
{
    "fault": {
        "faultstring": "JSONToXML[class java.lang.Integer]: Invalid source type class java.lang.Integer. Valid source types are [message, string].",
        "detail": {
            "errorcode": "steps.json2xml.InvalidSourceType"
        }
    }
}
```


### Cause

This error occurs if the type of the variable used to define the `<Source>` element is invalid.The valid types of variable are `message` and `string`. 

### Diagnosis


1.  Identify the invalid source type used in the JSON to XML policy. You can find this information in the error message. For example, in the following error, the invalid type is Integer. 

    ```
    "faultstring": "JSONToXML[class java.lang.Integer]: Invalid source type class java.lang.Integer. Valid source types are [message, string]."
    ```


1.  Examine all the JSON to XML policies in the specific API Proxy where the failure has occurred. In the failed JSON to XML Policy, note down the name of the variable specified in `<Source>`.

    Here's a sample policy that has the variable named EmployeeID specified in the `<Source>` element:



```
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<JSONToXML async="false" continueOnError="false" enabled="true" name="JSONToXML">
    <DisplayName>Check_SourceType</DisplayName>
    <Properties/>
    <Options>
        <NullValue>NULL</NullValue>
        <NamespaceBlockName>#namespaces</NamespaceBlockName>
        <DefaultNamespaceNodeName>$default</DefaultNamespaceNodeName>
        <NamespaceSeparator>:</NamespaceSeparator>
        <TextNodeName>#text</TextNodeName>
        <AttributeBlockName>#attrs</AttributeBlockName>
        <AttributePrefix>@</AttributePrefix>
        <InvalidCharsReplacement>_</InvalidCharsReplacement>
        <ObjectRootElementName>Root</ObjectRootElementName>
        <ArrayRootElementName>Array</ArrayRootElementName>
        <ArrayItemElementName>Item</ArrayItemElementName>
    </Options>
    <OutputVariable>request</OutputVariable>
    <Source>EmployeeID</Source>
</JSONToXML>
```

1.  Determine the type of variable specified in the `<Source>` element:
    1.  Locate the code within the API Proxy where this variable was defined first.
    1.  Once you figure out the policy in which the variable is defined and populated first, you need to determine the type of that variable as follows:
        1.  Check the value of the type attribute (if present).
        1.  If the type attribute is not present, then the variable is considered to be a string.
    1.  If the type of the variable specified in `<Source>` is neither message nor string type, then that's the cause of the error. You can learn about common variables and their types in the [Variables reference](https://docs.apigee.com/api-platform/reference/variables-reference).

    As an example, let us consider that ExtractVariables policy is used to extract the value from a JSON payload and sets the value to the variable `EmployeeID` of integer type as shown below:


    ```
    <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
    <ExtractVariables name="ExtractJSONVariables">
        <Source>request</Source>
        <JSONPayload>
            <Variable name="EmployeeID" type="integer">
                <JSONPath>$.ID</JSONPath>
            </Variable>
        </JSONPayload>
    </ExtractVariables>
    ```

    Now, recall that the `EmployeeID` variable is used in the `<Source>` element of the JSONToXML policy:

    ```
    <Source>EmployeeID</Source>
    ```

    Because the type of this variable is `Integer`, which is not a valid `<Source>` type, the API Proxy fails with the error:


  ```
  steps.json2xml.InvalidSourceType
  ```


### Resolution

Ensure that the type of the variable used to specify the `<Source>` element is
  valid. The valid `<Source>` types are `message` and `string`. 

In order to avoid the above error with JSONToXML policy, you could use the request
  variable which is of type message or any other string which is a valid JSON payload.

{% endblock %}
