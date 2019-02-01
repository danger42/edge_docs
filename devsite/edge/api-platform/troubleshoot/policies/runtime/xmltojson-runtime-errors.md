{% extends "_base.html" %} {% block title %}XML to JSON policy runtime error troubleshooting{% endblock %} {% block body %}


## SourceUnavailable


### Error code


```
steps.xml2json.SourceUnavailable
```



### Error response body


<pre class="prettyprint">
{
    "fault": {
        "faultstring": "XMLToJSON[<var>policy_name</var>]: Source [<var>source_variable</var>] is not available",
        "detail": {
            "errorcode": "steps.xmltojson.SourceUnavailable"
        }
    }
}
</pre>



### Example Error Message


```
{
    "fault": {
        "faultstring": "XMLToJSON[Convert-XMLToJSON]: Source response is not available",
        "detail": {
            "errorcode": "steps.xml2json.SourceUnavailable"
        }
    }
}
```



### Cause

This error occurs if the [message](https://docs.apigee.com/api-platform/reference/variables-reference#messagevariables) or string variable specified in the `<Source>` element of the XML to JSON policy is either:



*   out of scope (not available in the specific flow where the policy is being executed) or 
*   can't be resolved (is not defined)

For example, this error occurs if the XML to JSON policy is supposed to be executed in the request flow, but the `<Source>` element is set to the `response` variable, which doesn't exist in the request flow.


### Diagnosis



1.  Identify the XML to JSON policy where the error occurred and the name of the variable that is not available. You can find both of these items in the `faultstring` element of the error response. For example, in the following `faultstring`, the policy name is `Convert-XMLToJSON` and the variable is `response`:

  ```
  "faultstring": "XMLToJSON[Convert-XMLToJSON]: Source response is not available"
  ```


1.  In the failed XML to JSON policy XML, verify that the name of the variable set in the `<Source>` element matches the variable name identified in the fault string (step #1 above). For example, the following XML to JSON policy specifies a variable named `response` in the `<Source>` element, which matches what's in the `faultstring`:

  ```
  <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
  <XMLToJSON async="false" continueOnError="false" enabled="true" name="Convert-XMLToJSON">
      <DisplayName>Convert-XMLToJSON</DisplayName>
      <Properties/>
      <Format>google</Format>
      <OutputVariable>response</OutputVariable>
      <Source>response</Source>
  </XMLToJSON>
  ```


1.  Determine if the variable used in the `<Source>` element is defined and available in the flow in which the XML to JSON policy is being executed.
1.  If the variable is either: 
    *   out of scope (not available in the specific flow where the policy is being executed) or 
    *   can't be resolved (is not defined)

    then that's the cause for the error.


    As an example, let's say the XML to JSON policy shown above is supposed to be executed in the **request** flow.  Recall that the `response` variable is used in the `<Source>` element of the XML to JSON policy.  The response variable is available only in the response flow.


    Because the response variable does not exist in the request flow, you receive the error code: 


    ```
    steps.xml2json.SourceUnavailable
    ```



### Resolution

Ensure that the variable set in the `<Source>` element of the failed XML to JSON policy, is defined and exists in the flow where the policy executes.

To correct the example XML to JSON policy shown above, you could modify the `<Source>` element to use the `request` variable as it exists in the request flow:


```
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<XMLToJSON async="false" continueOnError="false" enabled="true" name="Convert-XMLToJSON">
    <DisplayName>Convert-XMLToJSON</DisplayName>
    <Properties/>
    <Format>google</Format>
    <OutputVariable>response</OutputVariable>
    <Source>request</Source>
</XMLToJSON>
```


## ExecutionFailed


### Error code


```
steps.xml2json.ExecutionFailed
```



### Error response body


<pre class="prettyprint">
{
    "fault": {
        "faultstring": "XMLToJSON[<var>policy_name</var>]: Execution failed. reason: Premature end of document while parsing at line [<var>line_number</var>](possibly  around char [<var>character_number</var>])",
        "detail": {
            "errorcode": "steps.xml2json.ExecutionFailed"
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
   <td>The input payload (XML) is empty.
   </td>
  </tr>
  <tr>
   <td>

<a href="#cause_invalid_or_malformed_input">Invalid or Malformed Input</a>
   </td>
   <td>The input (XML) passed to XML to JSON policy is invalid or malformed.
   </td>
  </tr>
</table>



### Cause: Missing input payload

In the XML to JSON policy if the content (payload) of the variable specified in the `<Source>` element is empty, then this error occurs.

For example, if the `<Source>` element in the XML to JSON policy is set as a `request` or `response` variable and it is supposed to contain an XML payload, this error occurs if the payload is empty.


### Diagnosis



1.  Identify the XML to JSON policy where the error occurred. You can find this information in the `faultstring` element of the error response. For example, in the following `faultstring`, the policy name is `Convert-XMLToJSON`:

    ```
    "faultstring": "XMLToJSON[Convert-XMLToJSON]: Execution failed. reason: Premature end of document while parsing at line 1(possibly  around char 0)"
    ```


1.  Examine the `<Source>` element in the failing XML to JSON policy XML and determine the variable specified. For example, the following XML to JSON policy has `<Source>` element set to request:


  ```
  <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
      <XMLToJSON async="false" continueOnError="false" enabled="true" name="Convert-XMLToJSON">
      <DisplayName>Convert-XMLToJSON</DisplayName>
      <Properties/>
      <Options>
          <RecognizeNumber>true</RecognizeNumber>
          <RecognizeBoolean>true</RecognizeBoolean>
          <RecognizeNull>true</RecognizeNull>
      </Options>
      <OutputVariable>request</OutputVariable>
      <Source>request</Source>
  </XMLToJSON>
  ```



1.  Check if the variable specified for the `<Source>` element in the XMLToJSON policy is empty.  If it is empty, then that's the cause for the error.

    In the example XML to JSON policy shown above, the request payload (i.e, request body) that was sent by the client was empty.


    For example:


    ```
    curl -v "http://<org>-<env>.apigee.net/v1/testxmltojson" -H "Content-Type: application/xml"
    ```

    Because the XML request payload is empty, you receive the error code:

    ```
    steps.xml2json.ExecutionFailed
    ```

    This error can also occur if the `<Source>` element is set to response, but an empty payload is passed by the backend server.

    Note: The HTTP Content-type header of the source message must be set to **application/xml**, otherwise the policy is not enforced, meaning it is skipped during processing.


### Resolution

Ensure that the input passed to XML to JSON policy via the `<Source>` element is a valid XML payload and non-empty.

To fix the issue with the sample XML to JSON policy, pass a valid XML payload. For example:




1.  Create a file named city.xml with the following contents:

    ```
    <?xml version="1.0" encoding="UTF-8"?>
    <root>
       <City>Bengaluru</City>
       <Name>Apigee</Name>
       <Pincode>560016</Pincode>
    </root>
    ```


1.  Make the API call using a cURL command as follows:

    ```
    curl -v "http://<org>-<env>.apigee.net/v1/testxmltojson" -H "Content-Type: application/xml" -X POST -d @company.xml
    ```



### Cause: Invalid or Malformed Input

If the XML to JSON policy parses input that is invalid or malformed, then you get this error.

For example, if the following invalid XML is provided as input to the XML to JSON policy, 


```
<?xml version="1.0" encoding="UTF-8"?>
<root>
   <City>Bengaluru</City>
   <Name>Apigee</Name>
   <Pincode>560016</Pincode>
```


you will get the error:


```
"faultstring": "XMLToJSON[Convert-XMLToJSON]: Execution failed. reason: Premature end of document while parsing at line 6(possibly  around char 0)"
```



### Diagnosis



1.  Identify the XML to JSON policy where the error occurred. You can find this information in the `faultstring` element of the error response. For example, in the following `faultstring`, the policy name is `Convert-XMLToJSON`:

    ```
    "faultstring": "XMLToJSON[Convert-XMLToJSON]: Execution failed. reason: Premature end of document while parsing at line 6(possibly  around char 0)"
    ```


1.  Examine the `<Source>` element specified in the failing XML to JSON policy XML. For example, the following XML to JSON policy has the `<Source>` element set to the `request` variable:


  ```
  <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
      <XMLToJSON async="false" continueOnError="false" enabled="true" name="Convert-XMLToJSON">
      <DisplayName>Convert-XMLToJSON</DisplayName>
      <Properties/>
      <Options>
          <RecognizeNumber>true</RecognizeNumber>
          <RecognizeBoolean>true</RecognizeBoolean>
          <RecognizeNull>true</RecognizeNull>
      </Options>
      <OutputVariable>request</OutputVariable>
      <Source>request</Source>
  </XMLToJSON>
  ```



1.  Validate if the input specified in the `<Source>` element to the XML To JSON policy is a valid XML payload. If the input is invalid or malformed, then that's the cause for the error.

    In the example XML to JSON policy shown above, the following invalid XML was passed to Extract Variables policy via the file `city.xml`:


    ```
    <?xml version="1.0" encoding="UTF-8"?>
    <root>
       <City>Bengaluru</City>
       <Name>Apigee</Name>
       <Pincode>560016</Pincode>
    ```



    Here's the sample API call that shows how the request was passed:


    ```
    curl -v "http://<org>-<env>.apigee.net/v1/testxmltpjson" -H "Content-Type: application/xml" -X POST -d @city.xml
    ```



    The XML payload passed to the API is invalid, as the XML does not have an end tag for `<root>` element. So you receive the error code:



  ```
  steps.xml2json.ExecutionFailed
  ```



  This error can also occur if the `<Source>` element was set to response but the XML response payload from the backend server is invalid or malformed.


  Note: The HTTP Content-type header of the source message must be set to **application/xml**, otherwise the policy is not enforced, meaning it is skipped during processing.


### Resolution

Ensure that the input passed to XML to JSON policy via the `<Source>` element is valid and not malformed.

To fix the issue with the sample XML to JSON policy discussed above, pass a valid XML payload request as follows:


```
<?xml version="1.0" encoding="UTF-8"?>
<root>
   <City>Bengaluru</City>
   <Name>Apigee</Name>
   <Pincode>560016</Pincode>
</root>
```



## OutputVariableIsNotAvailable


### Error code


```
steps.xml2json.OutputVariableIsNotAvailable
```



### Error response body


<pre class="prettyprint">
{
    "fault": {
        "faultstring": "XMLToJSON[<var>policy_name</var>]: Output variable is not available.",
        "detail": {
            "errorcode": "steps.xml2json.OutputVariableIsNotAvailable"
        }
    }
}
</pre>



### Example Error Message


```
{
    "fault": {
        "faultstring": "XMLToJSON[Convert-XMLToJSON]: Output variable is not available.",
        "detail": {
            "errorcode": "steps.xml2json.OutputVariableIsNotAvailable"
        }
    }
}
```



### Cause

This error occurs if the variable specified in the `<Source>` element of the XML to JSON policy is of type string and the `<OutputVariable>` element is not defined. The `<OutputVariable>` element is mandatory when the variable defined in the `<Source>` element is of type `string`. 

Note: The `<OutputVariable>` is not mandatory if the `<Source>` element is of type `message`.


### Diagnosis



1.  Identify the XML to JSON Policy where the error has occurred. You can find this in the `faultstring` element of the error response. For example, in the following `faultstring`, the policy name is `Convert-XMLToJSON`:

    ```
    "faultstring": "XMLToJSON[Convert-XMLToJSON]: Output variable is not available."
    ```


1.  In the failed XML to JSON Policy validate if the `<OutputVariable>` is missing. 

  Here's a sample XML to JSON Policy that has missing  `<OutputVariable>` element.


  ```
  <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
  <XMLToJSON async="false" continueOnError="false" enabled="true" name="Convert-XMLToJSON">
      <DisplayName>Convert-XMLToJSON</DisplayName>
      <Properties/>
      <Format>google</Format>
      <Source>TrackingNumber</Source>
  </XMLToJSON>

  ```


1.  Determine the type of variable specified in the `<Source>` element:
    1.  Locate the code within the API Proxy bundle, where the variable was defined first.
    1.  Once you figure out the policy in which the variable is defined and populated first, you need to determine the type of that variable as follows:
        1.  Check the value of the type attribute (if present).
        1.  If the type attribute is not present, then the variable is considered to be a string.
    1.  If the variable's type is `string`, then that's the cause of the error. You can learn about common variables and their types in the [Variables reference](https://docs.apigee.com/api-platform/reference/variables-reference).

    For example, look at the TrackingNumber variable in the above XML to JSON policy. It is of type string. Now, consider an Assign Message policy that is used to set value to a  variable called `TrackingNumber` as shown below:


    ```
    <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
    <AssignMessage async="false" continueOnError="false" enabled="true" name="Assign_TrackingNumber">
        <DisplayName>Assign_TrackingNumber</DisplayName>
        <Properties/>
        <AssignVariable>
            <Name>TrackingNumber</Name>
            <Value><![CDATA[<Code>560075393539898</Code>]]></Value>
            <Ref/>
        </AssignVariable>
        <IgnoreUnresolvedVariables>true</IgnoreUnresolvedVariables>
        <AssignTo createNew="false" transport="http" type="request"/>
    </AssignMessage>
    ```



    Note that the variable type set via `<AssignVariable>` is string. So the variable `TrackingNumber` is of type string. 


    Now, recall that the `TrackingNumber` variable is used in the `<Source>` element of the XML To JSON policy:


    ```
    <Source>TrackingNumber</Source>
    ```



    Since `TrackingNumber` is of string type and the `<OutputVariable>` is missing in the policy, you receive the error code: 



  ```
  steps.xml2json.OutputVariableIsNotAvailable
  ```



### Resolution

Ensure that if the variable specified in the `<Source>` element of the XMLToJSON Policy is of type string, the `<OutputVariable>` element is mandatory in this case.

To correct the XML To JSON Policy discussed above, include the `<OutputVariable>` element as shown below.


```
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<XMLToJSON async="false" continueOnError="false" enabled="true" name="Convert-XMLToJSON">
    <DisplayName>Convert-XMLToJSON</DisplayName>
    <Properties/>
    <Format>google</Format>
    <OutputVariable>response</OutputVariable>
    <Source>TrackingNumber</Source>
</XMLToJSON>
```



## InCompatibleTypes


### Error code


```
steps.xml2json.InCompatibleTypes
```



### Error response body


<pre class="prettyprint">
{
    "fault": {
        "faultstring": "XMLToJSON[<var>policy_name</var>]: String can not be assigned to message type.",
        "detail": {
            "errorcode": "steps.xml2json.InCompatibleTypes"
        }
    }
}
</pre>



### Example Error Message


```
{
    "fault": {
        "faultstring": "XMLToJSON[XMLToJSON_CheckType]: String can not be assigned to message type.",
        "detail": {
            "errorcode": "steps.xml2json.InCompatibleTypes"
        }
    }
}
```



### Cause

This error occurs if the type of the variable defined in the `<Source>` element and the `<OutputVariable>` element are not the same. It is mandatory that the type of the variables contained within the `<Source>` element and the `<OutputVariable>` element matches. 


```
The valid types are message and string. 
```



### Diagnosis



1.  Identify the XML to JSON Policy where the error has occurred. You can find this in the `faultstring` element of the error response. For example, in the following `faultstring`, the policy name is `XMLToJSON_CheckType`:

    ```
    "faultstring": "XMLToJSON[XMLToJSON_CheckType]: String can not be assigned to message type."
    ```


1.  In the failed XML To JSON Policy note the values specified in `<OutputVariable>`.

  Here's a sample XMLToJSON policy that has missing  `<OutputVariable>` element


  ```
  <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
  <XMLToJSON async="false" continueOnError="false" enabled="true" name="XMLToJSON_CheckType">
      <DisplayName>XMLToJSON_CheckType</DisplayName>
      <Properties/>
      <Format>google</Format>
      <OutputVariable>request</OutputVariable>
      <Source>TrackingNumber</Source>
  </XMLToJSON>
  ```



1.  Determine the type of variable specified in `<Source>` and `<OutputVariable>` elements:
    1.  Locate the code within the API Proxy bundle, where each of these variables were defined first.
    1.  Once you figure out the policy in which the variable is defined and populated first, you need to determine the type of that variable as follows:
        1.  Check the value of the type attribute (if present).
        1.  If the type attribute is not present, then the variable is considered to be a string.
    1.  If the type of the variable specified in `<Source>` is `string` while the type of `<OutputVariable>` is message or vice versa, then that's the cause of the error. You can learn about common variables and their types in the [Variables reference](https://docs.apigee.com/api-platform/reference/variables-reference).

    As an example, consider an Assign Message policy that is used to set a value to a  variable called `TrackingNumber` as shown below:


    ```
    <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
    <AssignMessage async="false" continueOnError="false" enabled="true" name="Assign_TrackingNumber">
        <DisplayName>Assign_TrackingNumber</DisplayName>
        <Properties/>
        <AssignVariable>
            <Name>TrackingNumber</Name>
            <Value><![CDATA[<Code>560075393539898</Code>]]></Value>
            <Ref/>
        </AssignVariable>
        <IgnoreUnresolvedVariables>true</IgnoreUnresolvedVariables>
        <AssignTo createNew="false" transport="http" type="request"/>
    </AssignMessage>
    ```



    Note that the variable type set via `<AssignVariable>` is string. So the variable `TrackingNumber` is of type string. 


    Now, recall that the `TrackingNumber` variable is used in the `<Source>` element of the XMLToJSON policy:


    ```
    <Source>TrackingNumber</Source>
    ```



    Similarly, recall that the `request` variable is used in the `<OutputVariable>` element of the XML To JSON policy:



    ```
    <OutputVariable>request</OutputVariable>
    ```



    Because `TrackingNumber` is of type `string` while the `response` variable is of type `message`, they are incompatible types so you receive the error code:



  ```
  steps.xml2json.InCompatibleTypes
  ```



  The above error can also occur if the variable in the `<Source>` element is of type `message` but the variable in the `<OutputVariable>` element is of type string. 


### Resolution

Ensure that the type of the variable defined in the `<Source>` element and the `<OutputVariable>` element are always the same. It is mandatory that the type of the variables contained within the `<Source>` element and the `<OutputVariable>` element matches. 

To correct the XML To JSON Policy discussed above, you could declare another variable `TrackingNumber_output` of type `string` using the Assign Message Policy and use this variable in the `<OutputVariable>` element of the XML To JSON Policy. 

Modified Assign Message policy:


```
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<AssignMessage async="false" continueOnError="false" enabled="true" name="Assign_TrackingNumber">
    <DisplayName>Assign_TrackingNumber</DisplayName>
    <Properties/>
    <AssignVariable>
        <Name>TrackingNumber</Name>
        <Value><![CDATA[<Code>560098</Code>]]></Value>
        <Ref/>
    </AssignVariable>
    <AssignVariable>
        <Name>TrackingNumber_output</Name>
        <Ref/>
    </AssignVariable>
    <IgnoreUnresolvedVariables>true</IgnoreUnresolvedVariables>
    <AssignTo createNew="false" transport="http" type="request"/>
</AssignMessage>
```


Modified XMLToJSON policy:


```
  <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
  <XMLToJSON async="false" continueOnError="false" enabled="true" name="XMLToJSON_CheckType">
      <DisplayName>XMLToJSON_CheckType</DisplayName>
      <Properties/>
      <Format>google</Format>
      <OutputVariable>TrackingNumber_output</OutputVariable>
      <Source>TrackingNumber</Source>
  </XMLToJSON>
```



## InvalidSourceType


### Error code


```
steps.xml2json.InvalidSourceType
```



### Error response body


<pre class="prettyprint">
{
    "fault": {
        "faultstring": "XMLToJSON[class <var>invalid_class</var>]: Invalid source type class <var>invalid_class</var>. Valid source types are [message, string].",
        "detail": {
            "errorcode": "steps.xml2json.InvalidSourceType"
        }
    }
}
</pre>



### Example Error Message


```
{
    "fault": {
        "faultstring": "XMLToJSON[class java.lang.Integer]: Invalid source type class java.lang.Integer. Valid source types are [message, string].",
        "detail": {
            "errorcode": "steps.xml2json.InvalidSourceType"
        }
    }
}
```



### Cause

This error occurs if the type of the variable used to define the `<Source>` element is invalid.The valid types of variable are `message` and `string`. 


### Diagnosis



1.  Identify the invalid source type used in the XML to JSON policy. You can find this information from the error message. For example, in the following error, the invalid type is integer. 

    ```
    "faultstring": "XMLToJSON[class java.lang.Integer]: Invalid source type class java.lang.Integer. Valid source types are [message, string]."
    ```


1.  Examine all the XML to JSON policies in the specific API Proxy where the failure has occurred. In the failed XML To JSON Policy note the name of the variable specified in `<Source>`. 

  ```
  <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
  <XMLToJSON async="false" continueOnError="false" enabled="true" name="XMLToJSON_CheckType">
      <DisplayName>XMLToJSON_CheckType</DisplayName>
      <Properties/>
      <Format>google</Format>
      <OutputVariable>response</OutputVariable>
      <Source>BookCode</Source>
  </XMLToJSON>
  ```


1.  Determine the type of variable specified in `<Source>` element:
    1.  Locate the code within the API Proxy bundle, where this variable was defined first.
    1.  Once you figure out the policy in which the variable is defined and populated first, you need to determine the type of that variable as follows:
        1.  Check the value of the type attribute (if present).
        1.  If the type attribute is not present, then the variable is considered to be a string.
    1.  If the type of the variable specified in `<Source>` is neither `message` nor `string` type, then that's the cause of the error. You can learn about common variables and their types in the [Variables reference](https://docs.apigee.com/api-platform/reference/variables-reference).

    As an example,  consider an ExtractVariables policy that is used to extract the value from an XML payload and sets the value to the variable `BookCode` to be `integer` type as shown below:



  ```
  <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
  <ExtractVariables async="false" continueOnError="false" enabled="true" name="Extract_BookCode">
      <DisplayName>Extract_BookCode</DisplayName>
      <Properties/>
      <Source>request</Source>
      <XMLPayload stopPayloadProcessing="false">
          <Variable name="BookCode" type="integer">
              <XPath>/root/BookCode</XPath>
          </Variable>
      </XMLPayload>
  </ExtractVariables>
  ```



  Now, recall that the `BookCode` variable is used in the `<Source>` element of the XML To JSON policy:


  ```
  <Source>BookCode</Source>
  ```



  Because the type of this variable is `Integer`, which is not a valid `<Source>` type, the API Proxy fails with the error:


  ```
  steps.xml2json.InvalidSourceType
  ```



### Resolution

Ensure that the type of the variable used to specify the `<Source>` element is valid. The valid `<Source>` types are `message` and `string`. 

To avoid the above error with XML To JSON policy, you could use the `request` variable which is of type `message` or any other string which is a valid XML payload.

{% endblock %}
