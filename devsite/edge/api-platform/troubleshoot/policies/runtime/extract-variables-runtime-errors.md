{% extends "_base.html" %} {% block title %}Extract Variables runtime error troubleshooting{% endblock %} {% block body %}

<aside class="note">Was this troubleshooting playbook helpful? Please let us know
  by clicking <a href="" class="google-feedback"
   data-p="1636213" data-b="docsite" data-context="troubleshooting-feedback">
  Send Feedback 
</a>.</aside>


## SourceMessageNotAvailable



### Error code


```
steps.extractvariables.SourceMessageNotAvailable
```



### Error response body


<pre>
{
  "fault": {
      "faultstring": "[<b><i>variable_name</b></i>] message is not available for ExtractVariable: [<b><i>policy_name</b></i>]",
      "detail": {
          "errorcode": "steps.extractvariables.SourceMessageNotAvailable"
      }
  }
}
</pre>



### Cause

This error occurs if the [message](/api-platform/reference/variables-reference#messagevariables) variable specified in the `<Source>` element of the [Extract Variables policy](/api-platform/reference/policies/extract-variables-policy#sourceelement) is either:



*   out of scope (not available in the specific flow where the policy is being executed) or 
*   can't be resolved (is not defined)

For example, this error occurs if the Extract Variables policy executes in the
request flow, but the `<Source>` element is set to the `response` or `error`
variable, which doesn't exist in the request flow.


### Diagnosis



1.  Identify the Extract Variables policy where the error occurred and the name of the variable that is not available. You can find both of these items in the `faultstring` element of the error response. For example, in the following faultstring, the policy name is `ExtractVariables-1` and the variable is `response`:

   
    `"faultstring": "response message is not available for ExtractVariable: ExtractVariables-1"`



1.  In the failed Extract Variables policy XML, verify that the name of the variable
set in the element matches the variable name identified in the fault string (step #1 
above). For example, the following Extract Variables policy specifies a variable 
named `response` in the element, which matches what's in the fault string:

    ```
    <ExtractVariables name="ExtractVariables-1">
        <Source>response</Source>
        <URIPath>
            <Pattern ignoreCase="true">/a/{pathSeg}</Pattern>
        </URIPath>
        <VariablePrefix>urirequest</VariablePrefix>
    </ExtractVariables>
    ```


1.  Determine if the variable used in the `<Source> `element is defined and available in the flow in which the Extract Variables policy is being executed.
1.  If the variable is either: 
    *   out of scope (not available in the specific flow where the policy is being executed) or 
    *   can't be resolved (is not defined)

    then that's the cause for the error.


    As an example, let's say the Extract Variables policy shown above executes in the **request** flow.  Recall that the `response`  variable is used in the `<Source>` element of the Extract Variables policy.  The `response` variable is available only in the response flow.


    Since the `response` variable does not exist in the request flow, you receive the error code: `steps.extractvariables.SourceMessageNotAvailable`.



### Resolution

Ensure that the variable set in the `<Source>` element of the failed Extract Variables policy, is defined and exists in the flow where the policy executes.

To correct the example Extract Variables policy shown above, you could modify the `<Source>` element to use the `request` variable as it exists in the request flow:


```
<ExtractVariables name="ExtractVariables-1">
    <Source>request</Source>
    <URIPath>
        <Pattern ignoreCase="true">/a/{pathSeg}</Pattern>
    </URIPath>
    <VariablePrefix>urirequest</VariablePrefix>
</ExtractVariables>
```



## SetVariableFailed



### Error code


```
steps.extractvariables.SetVariableFailed
```



### Error response body


<pre>
{
    "fault": {
        "faultstring": "Failed to set variable [<b><i>variable_name</b></i>] value [<b><i>variable_value</b></i>] from ExtractVariables: [<b><i>policy_name</b></i>]",
        "detail": {
            "errorcode": "steps.extractvariables.SetVariableFailed"
        }
    }
}
</pre>



### Cause

This error occurs if the Extract Variables policy could not set the value to a variable.  It generally happens if you try to assign values to multiple variables whose names start with the same words in a nested dot-separated format.

For example, let's say you create/assign a value to a variable `var.color`.  In this case, `color` is allocated as an object node. If you then try to assign a value to another variable `var.color.next`, it will fail because `color` is already allocated, and it is not permitted to allocate another variable to it.


### Diagnosis



1.  Identify the Extract Variables policy where the error occurred and the name of the variable for which the value could not be set. You can find both of these items in the `faultstring` element of the error response. For example, in the following fault string, the policy name is `ExtractColors` and the variable is `var.color.next`:


    `"faultstring": "Failed to set variable var.color.next value red from ExtractVariables: ExtractColors"`


1.  In the failed Extract Variables policy XML, verify that the name of the variable
matches the variable name identified in the fault string (step #1 above). For
example, the following policy attempts to assign a value from a request query
parameter to a variable named `var.color.next` (the value that appears the fault string):

    ```
    <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
    <ExtractVariables name="ExtractColors">
        <DisplayName>ExtractColors</DisplayName>
        <Source>request</Source>
        <QueryParam name="color">
            <Pattern ignoreCase="true">{color}</Pattern>
        </QueryParam>
        <QueryParam name="nextcolor">
            <Pattern ignoreCase="true">{color.next}</Pattern>
        </QueryParam>
        <VariablePrefix>var</VariablePrefix>
    </ExtractVariables>
    ```


1.  Examine all the variable names used in the failed Extract Variables policy. If you have assigned a value to another variable whose name starts with the same name as the variable name identified in the fault string (step #1 above), then that's the cause of the error.

  In the example Extract Variables policy shown above, notice that:

  *   The value of the query parameter named `color` is first assigned to a variable <code>var.color<strong> </strong></code>(<strong>Note:</strong> `var` is the prefix for all the variables as set in the `<VariablePrefix>` element)
  *   In the next assignment, the value of the <code>nextcolor</code> query parameter is assigned to another variable <code>var.color.next</code>. 
  *   Since the <code>var.color</code> is already allocated, the Extract Variables policy cannot allocate another nested variable <code>var.color.next</code>.  So you receive the error code: <code>steps.extractvariables.SetVariableFailed</code>


### Resolution



1.  Ensure that you don't have multiple variable names whose names start with the
same words that are nested in dot-separated format. 

    To correct the Extract Variables policy shown above, you can modify the
    variable name `var.color.next` to use the variable name  `var.nextcolor.`


    ```
    <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
    <ExtractVariables name="ExtractColors">
        <DisplayName>ExtractColors</DisplayName>
        <Source>request</Source>
        <QueryParam name="color">
            <Pattern ignoreCase="true">{color}</Pattern>
        </QueryParam>
        <QueryParam name="nextcolor">
            <Pattern ignoreCase="true">{nextcolor}</Pattern>
        </QueryParam>
        <VariablePrefix>var</VariablePrefix>
    </ExtractVariables>

    ```



### More Information

For more details refer to this [community post](https://community.apigee.com/questions/25299/a-variable-called-varcolour-is-fine-but-creating-a.html).




## InvalidJSONPath



### Error code


```
steps.extractvariables.InvalidJSONPath
```



### Error response body


<pre>
{
    "fault": {
        "faultstring": "Invalid JSON path [<b><i>path_name</b></i>] in policy [<b><i>policy_name</b></i>].",
        "detail": {
            "errorcode": "steps.extractvariables.InvalidJSONPath"
        }
    }
}
</pre>



### Cause

This error occurs if an invalid JSON path is used in the `<JSONPath>` element
of the Extract Variables policy. For example, if a JSON payload does not have
the object `Name`, but you specify `Name` as the path in the Extract Variables
policy, then this error occurs.


### Diagnosis



1.  Identify the Extract Variables policy where the error occurred and the invalid
JSON path. You can find both of these items in the `faultstring` element of the
error response. For example, in the following fault string, the policy name is
`ExtractJSONVariables` and the invalid JSON path is `$.Name`:

    `"faultstring": "Invalid JSON path $.Name in policy ExtractJSONVariables."`


1.  In the failed Extract Variables policy XML, verify that the JSON path set in
the `<JSONPath>` element matches the path identified in the fault string (step #1 above).
  For example, the following Extract Variables policy specifies the JSON path `$.Name`,
  which matches what's in the fault string:



    ```
    <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
    <ExtractVariables name="ExtractJSONVariables">
        <Source>response</Source>
        <JSONPayload>
            <Variable name="name" type="string">
                <JSONPath>$.Name</JSONPath>
            </Variable>
        </JSONPayload>
       <VariablePrefix>employee</VariablePrefix>
    </ExtractVariables>
    ```


1.  Examine the `<Source>` element and determine from which JSON Payload are you trying to extract the variable.  For example, if the `<Source>` element is set to `request`, then the policy is extracting the JSON payload from the request object. If it is set to `response`, then it would be response object.

    In the example Extract Variables policy shown above, the `<Source>` element is
    set to `response`, so the variables are being extracted from the JSON payload
    of the response.

    `<Source>response</Source>`


1.  Examine the appropriate JSON payload (determined from step #3) and validate
if it has the object specified in the `<JSONPath>` element.  If the JSON payload
does not have that object, then that's the cause for the error.

    
    As an example, consider that you are trying to extract variables from following
    JSON response payload:

    ```
    { 
      "firstName":"John",
      "lastName":"Doe",
      "city":"San Jose",
      "state":"CA"
    }
    ```



    Since the JSON response payload does not have the **<code>Name</code>** object, the Extract Variables policy fails with the error you receive the error code: <code>steps.extractvariables.InvalidJSONPath</code>.



### Resolution



1.  Ensure that only objects that are part of the JSON Payload from which the variables are being extracted are specified in the `<JSONPath>` element of Extract Variables policy.

    To correct the example Extract Variables policy shown above, you can modify the `<JSONPath>` element to specify one of the objects available in the sample JSON response Payload (for example, the objects `firstName` and `lastName` are valid):


    ```
    <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
    <ExtractVariables name="ExtractJSONVariables">
        <Source>response</Source>
        <JSONPayload>
            <Variable name="name" type="string">
                <JSONPath>$.firstName</JSONPath>
            </Variable>
        </JSONPayload>
       <VariablePrefix>employee</VariablePrefix>
    </ExtractVariables>

    ```


## ExecutionFailed



### Error code


```
steps.extractvariables.ExecutionFailed
```



### Error response body


<pre>
{
    "fault": {
        "faultstring": "Failed to execute the ExtractVariables: [<b><i>policy_name</b></i>]",
        "detail": {
            "errorcode": "steps.extractvariables.ExecutionFailed"
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
   <td><a href="#cause-missing-input-payload">Missing input payload</a>
   </td>
   <td>The input payload (JSON, XML) is empty.
   </td>
  </tr>
  <tr>
   <td><a href="#cause-invalid-or-malformed-input">Invalid or Malformed Input</a>
   </td>
   <td>The input (JSON, XML, etc) passed to the policy is invalid or malformed.
   </td>
  </tr>
</table>



### <a name="cause-missing-input-payload">Cause: Missing input payload</a>

If the Extract Variables policy is supposed to extract the variables from a JSON or XML payload, but the content (payload) of the variable specified in the `<Source>` element is empty, then this error occurs.

For example, If the `<Source>` element in the Extract Variables policy is set as `request` or `response `and they are supposed to contain or JSON or XML payload, but it the payload is empty, then the error occurs.


### Diagnosis



1.  Identify the Extract Variables policy where the error occurred. You can find
this information in the faultstring element of the error response. For example,
in the following faultstring, the policy name is `ExtractJSONVariables`:


    `"faultstring": "Failed to execute the ExtractVariables: ExtractXMLVariables"`



1.  Examine the `<Source>` element in the failing Extract Variables XML and determine the type of input from which the variables are being extracted. For example, the following Extract Variables policy has `<Source>` element set to `response` and is extracting the variables from the XML payload:


  ```
  <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
      <ExtractVariables name="ExtractXMLVariables" async="false" continueOnError="false" enabled="true">
      <XMLPayload>
          <Namespaces/>
          <Variable name="City" type="string">
              <XPath>/city</XPath>
          </Variable>
      </XMLPayload>
      <Source clearPayload="false">response</Source>
  </ExtractVariables>

  ```



1.  Check if the input that is being parsed by the Extract Variables policy is empty.  If the input is empty, then that's the cause for the error.

    In the example Extract Variables policy shown above, the response payload (i.e, response body) that was sent by the backend server was empty.


    Since the XML response payload is empty, you receive the error code:


    `steps.extractvariables.ExecutionFailed`



    This error can also occur if the `<Source>` element is set to `request`, but
    no payload is passed in the API proxy request. For example:


    ```
    curl -v "http://<org>-<env>.apigee.net/v1/testevexecutionfailed" -H "Content-Type: application/xml"
    ```



   The Extract Variables policy only performs XML extraction when the message's
   `Content-Type` header is `application/xml`, `text/xml` or `application/*+xml`.
   You must pass the Content-Type header as `application/xml`, `text/xml` or
   `application/*+xml` if you want the Extract Variables policy to parse an XML
   request payload.


### Resolution

Ensure that the input passed to Extract Variables policy is valid and non empty.

To fix the issue with the example Extract Variables policy shown above, pass a
valid XML payload. For example:



1.  Create a file named `city.xml` with the following contents:

    ```
    <city>Bengaluru</city>
    <area>Sanjaynagar</area>
    ```


1.  Make the API call using a cURL command as follows:

    ```
    curl -v "http://<org>-<env>.apigee.net/v1/testevexecutionfailed" -H "Content-Type: application/xml" -X POST -d @city.xml

    ```



### <a name="cause-invalid-or-malformed-input">Cause: Invalid or Malformed Input</a>

If the Extract Variables policy parses input that is invalid or malformed, then you get this error.

For example, if the following invalid JSON is provided as input to the Extract Variables policy, you will get this error.


```
[
    "args": ["name" : "amar" ]
]
```



### Diagnosis



1.  Identify the Extract Variables policy where the error occurred. You can find
this information in the `faultstring` element of the error response. For example,
in the following `faultstring`, the policy name is `ExtractJSONVariables`:


    `"faultstring": "Failed to execute the ExtractVariables: ExtractJSONVariables"`


1.  Examine the `<Source>` element in the failing Extract Variables XML and determine the type of input from which the variables are being extracted. For example, the following Extract Variables policy has the `<Source>` element set to `request` and is extracting the variables from the JSON payload:


  ```
  <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
      <ExtractVariables name="ExtractJSONVariables" async="false" continueOnError="false" enabled="true">
      <JSONPayload>
          <Variable name="Name" type="string">
              <JSONPath>$.args.name</JSONPath>
          </Variable>
      </JSONPayload>
      <Source clearPayload="false">request</Source>
  </ExtractVariables>

  ```



1.  Check if the input that is being parsed by the Extract Variables policy is valid.  If the input is invalid or malformed, then that's the cause for the error.

    In the example Extract Variables policy shown above, the following invalid JSON was passed to Extract Variables policy


    ```
    [
        "args": ["name" : "amar" ]
    ]
    ```



    Here's the sample API call that shows how the request was passed:


    ```
    curl -v "http://<org>-<env>.apigee.net/v1/testevexecutionfailed" -H "Content-Type: application/json" -X POST -d '[ "args" : ["name" : "amar" ]]'
    ```



    The JSON payload passed to the API is invalid as it contains an array with square brackets (`[ ]`) , so you receive the error code:



  ```
  steps.extractvariables.ExecutionFailed
  ```



    This error can also occur if the `<Source>` element was set to `response `but the JSON response payload is invalid or malformed.


    Note: The Extract Variables policy only performs JSON extraction when the
    message's `Content-Type` header is  `application/json`. You must pass the
    `Content-Type` header as `application/json` if you want the Extract Variables
    policy to parse a JSON request payload.


### Resolution

Ensure that the input passed to Extract Variables policy is valid and not malformed.

To fix the issue with example Extract Variables policy discussed above, pass a valid JSON payload request as follows:

```
{  
   "args":{  
      "name":"amar"
   }
}
```


## UnableToCast




### Error code


```
steps.extractvariables.UnableToCast
```



### Error response body


<pre>
{
    "fault": {
        "faultstring": "Unable to cast value [<b><i>value</b></i>] as [<b><i>type</b></i>].",
        "detail": {
            "errorcode": "steps.extractvariables.UnableToCast"
        }
    }
}
</pre>



### Cause

This error occurs if the Extract Variables policy was unable to cast the extracted
value to a variable. Typically this happens if you attempt to set the value of
one data type to a variable of another data type. 

For example, if the Extract Variable policy attempts to set a value that has been
extracted from a String variable to an Integer variable, then you will see this error.


### Diagnosis



1. Identify the data type of the variable to which the Extract Variables policy
is attempting to cast and fails. You can find this information in the `faultstring`
element of the error response. For example, in the following `faultstring`, the
object type is `BOOLEAN`:

  `"faultstring":"Unable to cast value 36.231 as BOOLEAN."`


1.  Identify the Extract Variables policy where you are trying to set the value to a variable of type determined in step #1.

    For example, the following Extract Variables policy extracts the value from
    a JSONPath `$.latitude` to a variable of type `boolean`, which matches what's
    in the fault string:




  ```
  <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
      <ExtractVariables name="EV-XML-Name" async="false" continueOnError="false" enabled="true">
      <JSONPayload>
        <Variable name="latitude" type="boolean">
            <JSONPath>$.latitude</JSONPath>
        </Variable>
      </JSONPayload>
      <Source clearPayload="false">response</Source>
  </ExtractVariables>

  ```



2. Examine the data type of the value that is being extracted. The data may be in
the form of headers, URI paths, JSON/XML payloads, form parameters, and query parameters.

3. Verify if the data type determined in step #3 and data type of the variable to
which data is being assigned (determined in step #2) are same.

4. If the data type of the source and destination variables is not same, then
that’s the cause for the error.


  In the example Extract Variables policy shown above, consider the JSON request
  payload is as follows:

  ```
  { 
   "latitude":36.2312 
  }
  ```

  The data type of the value being extracted from the `<JSONPath>`, `‘$.latitude’`,
  is an Integer, which is being assigned to a variable of data type boolean.

  Since the data type of the extracted value and data type of the variable to
  which the value is being assigned are not the same, you receive the error code:
  `steps.extractvariables.UnableToCast`.


### Resolution

Ensure that the data type of extracted value and the variable to which the content
is being assigned are of same type.

To correct the example Extract Variables policy, you have to modify the type of
the variable to Integer:


```
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<ExtractVariables name="EV-XML-Name" async="false" continueOnError="false" enabled="true">
    <JSONPayload>
        <Variable name="latitude" type="integer">
            <JSONPath>$.latitude</JSONPath>
        </Variable>
    </JSONPayload>
    <Source clearPayload="false">response</Source>
</ExtractVariables>
```




## JsonPathParsingFailure


### Error code


```
steps.extractvariables.JsonPathParsingFailure
```

### Error response body


<pre>
{
    "fault": {
        "faultstring": "ExtractVariables [<b><i>policy_name</b></i>]: Json path parsing failed for for flow variables [<b><i>variable_name</b></i>]",
        "detail": {
            "errorcode": "steps.extractvariables.JsonPathParsingFailure"
        }
    }
}
</pre>



### Cause

This error occurs when the Extract Variables
policy is unable to parse a JSON path and extract data from the flow variable
specified in `<Source>` element. Typically this happens if the flow variable
specified in the `<Source>` element does not exist in the current flow.

For example, this error occurs if the Extract Variables policy executes in the
response flow and is expects to parse a JSON path, but the `<Source>` element
specifies the flow variable `request.content`, which does not exist in the response flow.


### Diagnosis



1.  Identify the Extract Variables policy where the error occurred and the name of the flow variable to which data could not be extracted. You can find both of these items in the faultstring element of the error response. For example, in the following faultstring, the policy name is `ExtractVariables-1` and the variable is `request.content`:

    `"faultstring": "ExtractVariables ExtractJSONVariabes: Json path parsing failed for for flow variables request.content"`


1.  In the failed Extract Variables policy XML, verify that the name of the
variable set in the `<Source>` element matches the variable name identified in the
  fault string (step #1 above). For example, the following Extract Variables
  policy specifies a variable named `request.content`, which matches what's in the faultstring:


  ```
  <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
      <ExtractVariables name="ExtractJSONVariables" async="false" continueOnError="false" enabled="true">
      <JSONPayload>
          <Variable name="Name" type="string">
              <JSONPath>$.args.name</JSONPath>
          </Variable>
      </JSONPayload>
      <Source clearPayload="false">request.content</Source>
  </ExtractVariables>

  ```



1.  Validate if the flow variable used in the `<Source> `element is defined and available in the flow in which the Extract Variables policy is being executed.
1.  If the variable is either:
    *   out of scope (not available in the specific flow where the policy is being executed) or 
    *   can't be resolved (is not defined)

    then that's the cause for the error.


    As an example, let's say the ExtractVariables policy shown above is supposed to be executed in the response flow.  Recall that the flow variable <code>request.content<strong> </strong></code>is used in the `<Source>` element of the Extract Variables policy.  The flow variable <code>request.content</code> is available only in the request flow.


    Since the `response` variable does not exist in the request flow, you receive the error code: `steps.extractvariables.JsonPathParsingFailure`.



### Resolution

Ensure that the flow variable used in the `<Source> `element of the Extract
Variables policy is available in the flow in which the Extract Variables policy
executes.

For example, assume that a variable named `response.content` exists in the response
flow and in fact contains the JSON that you wish to extract. To correct the Extract
Variables policy shown above, you could modify the `<Source>` element as follows:


```
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<ExtractVariables name="ExtractJSONVariables" async="false" continueOnError="false" enabled="true">
    <JSONPayload>
        <Variable name="Name" type="string">
            <JSONPath>$.args.name</JSONPath>
        </Variable>
    </JSONPayload>
    <Source clearPayload="false">response.content</Source>
</ExtractVariables>
```

{% endblock %}
