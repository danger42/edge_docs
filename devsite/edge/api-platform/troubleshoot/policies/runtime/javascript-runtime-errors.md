{% extends "_base.html" %} {% block title %}JavaScript policy runtime error troubleshooting{% endblock %} {% block body %}

<aside class="note">Was this troubleshooting playbook helpful? Please let us know
  by clicking <a href="" class="google-feedback"
   data-p="1636213" data-b="docsite" data-context="troubleshooting-feedback">
  Send Feedback 
</a>.</aside>

## ScriptExecutionFailed


### Error code


```
steps.javascript.ScriptExecutionFailed
```



### Error response body


<pre>
{
    "fault": {
        "faultstring": "Execution of <var>javascript_policy_name</var> failed with error: <var>error_type</var>: <var>error_description</var>. (<var>javascript_source_file_name</var>)\"",
        "detail": {
            "errorcode": "steps.javascript.ScriptExecutionFailed"
        }
    }
}
</pre>



### Types of Errors and possible causes

The JavaScript policy can throw many different types of ScriptExecutionFailed errors.
Some of the more commonly seen errors are listed in the table below:


<table>
  <tr>
   <td><strong>Type of Error</strong>
   </td>
   <td><strong>Cause</strong>
   </td>
  </tr>
  <tr>
   <td>

<a href="#range_error">Range Error</a>
   </td>
   <td>A <strong>RangeError</strong> is thrown if you use a number that is outside the range of legal values.
   </td>
  </tr>
  <tr>
   <td>

<a href="#reference_error">Reference Error</a>
   </td>
   <td>A <strong>ReferenceError</strong> is thrown if you use (reference) a variable that has not been declared.
   </td>
  </tr>
  <tr>
   <td>

<a href="#syntax_error">Syntax Error</a>
   </td>
   <td>A <strong>SyntaxError</strong> is thrown if you try to evaluate code with a syntax error.
   </td>
  </tr>
  <tr>
   <td>

<a href="#type_error">Type Error</a>
   </td>
   <td>A <strong>TypeError</strong> is thrown if you use an operation that is outside the range of expected types.
   </td>
  </tr>
  <tr>
   <td>

<a href="#uri_error">URI Error</a>
   </td>
   <td>A <strong>URIError</strong> is thrown if you use illegal characters in a URI function.
   </td>
  </tr>
</table>



## Range error

The error type `RangeError` is thrown when you operate on a value or pass a value
to a function that is not in the set or range of allowed values. 

For example, this error is thrown under the following circumstances:



1.  If you use an invalid date such as Sep 31st 2018 with some of the Date APIs.
1.  If you pass an invalid value to numeric methods such as `Number.toPrecision()`, `Number.tofixed()`, or `Number.toExponential()`. For example, let's say you pass a large value like 400 or 500 in the `Number.toPrecision()` method you will see a range error.
1.  If you create an array with illegal length.


### Error response body


<pre>
{
    "fault": {
        "faultstring": "Execution of <var>javascript_policy_name</var> failed with error: Javascript runtime error: \"<b>RangeError</b>: <var>error_description</var>. (<var>javascript_source_file_name</var>:<var>line_number</var>)\"",
        "detail": {
            "errorcode": "steps.javascript.ScriptExecutionFailed"
        }
    }
}
</pre>


**Note:** The diagnosis and resolution of range errors depends on the exact error message thrown by the JavaScript policy.  A couple of examples are described below for your reference.


### Example 1: Invalid Date


#### Example error response body


```
{
    "fault": {
        "faultstring": "Execution of ParseDate failed with error: Javascript runtime error: \"RangeError: Date is invalid. (ParseDate.js:2)\"",
        "detail": {
            "errorcode": "steps.javascript.ScriptExecutionFailed"
        }
    }
}
```



#### Diagnosis



1.  Identify the JavaScript policy, its source file, the line number where the error occurred and the error description. You can find all this information in the `faultstring` element of the error response. For example, in the following `faultstring`, the JavaScript policy name is `ParseDate`, the JavaScript source file is `ParseDate.js`, the line number at which the error occurred is `2` and the error description is `Date is invalid`:


  ```
  "faultstring": "Execution of ParseDate failed with error: Javascript runtime error: \"RangeError: Date is invalid. (ParseDate.js:2)\""
  ```

1.  Examine the JavaScript source file (identified in step #1 above) and check if there is an invalid date being used in the line number specified in the error or if the variable used in the line number has an invalid date.  If an invalid date is used, then that's the cause of the error.

	

  Here's a sample JavaScript source file that leads to this error:


  **ParseDate.js**


  ```
  var date = new Date('2018-09-31T11:19:08.402Z'); 
  date.toISOString();
  ```



  In this example, there's a variable `date` used in the line number 2.  Examining the source file, you can see that the `date` variable is set with an invalid date: `2018-09-31T11:19:08.402Z.` This date is invalid because September does not have 31 days.


 **Note:** The ISO-8601 format used in this example is: `YYYY-MM-DDTHH:mm:ss.sssZ` 


#### Resolution

Ensure that you always use a valid date while using Date APIs in the JavaScript code.

To fix the example JavaScript code shown above, you can set the date as `Sept 30 2018` as shown below:


```
var date = new Date('2018-09-30T11:19:08.402Z'); 
date.toISOString();
```



### Example 2: Invalid number passed to Precision APIs


#### Example error response body


```
{
    "fault": {
        "faultstring": "Execution of SetNumberPrecision failed with error: Javascript runtime error: "RangeError: Precision 400 out of range. (SetNumberPrecision.js:2)\"",
        "detail": {
            "errorcode": "steps.javascript.ScriptExecutionFailed"
        }
    }
}
```



#### Diagnosis



1.  Identify the JavaScript policy, its source file,  the line number where the error occurred, and the error description. You can find all this information in the `faultstring` element of the error response. For example, in the following `faultstring`, the JavaScript policy name is **<code>SetNumberPrecision</code>**, the JavaScript source file is <strong><code>SetNumberPrecision.js</code></strong>, the line number at which the error occurred is <strong>2</strong> and the error description is <strong><code>Precision 400 out of range.</code></strong>


  ```
  "faultstring": "Execution of SetNumberPrecision failed with error: Javascript runtime error: "RangeError: Precision 400 out of range. (SetNumberPrecision.js:2)\""
  ```



1.  Examine the JavaScript source file (identified in step #1 above).  If the large number mentioned as part of the error description is used in the specific line number, then that's the cause for the error.

	

  Here's a sample JavaScript source file that leads to this error:


  **SetNumberPrecision.js**


  ```
  var number = 12.3456;
  var rounded_number = number.toPrecision(400);
  print("rounded_number = " + rounded_number);
  ```



  In this example, notice that a large value of 400 is used in line number 2. Because you can't set the precision to such a large number of digits, you get the below error:


  ```
  "faultstring": "Execution of SetNumberPrecision failed with error: Javascript runtime error: "RangeError: Precision 400 out of range. (SetNumberPrecision.js:2)\""
  ```



#### Resolution

Ensure that the number used in the `toPrecision()` method is within the set of allowed values.

To fix the issue with the example JavaScript described above, set the number of significant digits to 2 which is valid:


```
var number = 12.3456;
var rounded_number = number.toPrecision(2);
print("rounded_number = " + rounded_number);
```



## Reference error

The error type `ReferenceError` is thrown when an undefined variable in your JavaScript
is used (referenced) or operated on.


### Error response body


<pre>
{
    "fault": {
        "faultstring": "Execution of <var>javascript_policy_name</var> failed with error: Javascript runtime error: \"<b>ReferenceError</b>: <var>variable_name</var> is not defined. (<var>javascript_source_file_name</var>:<var>line_number</var>)\"",
        "detail": {
            "errorcode": "steps.javascript.ScriptExecutionFailed"
        }
    }
}
</pre>


### Example error response body

```
{
    "fault": {
        "faultstring": "Execution of ComputeTotalPrice failed with error: Javascript runtime error: \"ReferenceError: \"price\" is not defined. (ComputeTotalPrice.js:3)\"",
        "detail": {
            "errorcode": "steps.javascript.ScriptExecutionFailed"
        }
    }
}
```

#### Diagnosis


1.  Identify the JavaScript policy, its source file, and the line number where the undefined variable is referenced. You can find all this information in the `faultstring` element of the error response. For example, in the following `faultstring`, the JavaScript policy name is `ComputeTotalPrice`, the corresponding source file is `ComputeTotalPrice.js`, line number at which the error occurred is `3` and the undefined variable name is `price.`

    ```
    "faultstring": "Execution of ComputeTotalPrice failed with error: Javascript runtime error: \"ReferenceError: \"price\" is not defined. (ComputeTotalPrice.js:3)\""
    ```


1.  Examine the line number in the JavaScript source file and verify if the undefined variable identified in step #1 above, is being referenced.  For example, the following JavaScript code references the undefined variable `price` in line 3, which matches with what's in the faultstring:

  **ComputeTotalPrice.js**


  ```
  var item = context.getVariable("request.queryparam.item");
  var quantity = context.getVariable("request.queryparam.quantity");
  var totalprice = parseInt(quantity) * parseInt(price);
  context.setVariable("TotalPrice", totalprice);

  ```


1.  Check if the specific variable is defined within the JavaScript code. If the variable is not defined, then that's the cause of the error.

    In the example script shown above, the undeclared/defined `price` variable is used; therefore, you will see the below error:

    ```
    "faultstring": "Execution of ComputeTotalPrice failed with error: Javascript runtime error: \"ReferenceError: \"price\" is not defined. (ComputeTotalPrice.js:3)\""
    ```

#### Resolution

Ensure that all the variables referenced in the JavaScript code are properly defined. 

To fix the issue with the example JavaScript shown above, define the variable price before using it. For example:


```
var item = context.getVariable("request.queryparam.item");
var quantity = context.getVariable("request.queryparam.quantity");
var price = context.getVariable("request.queryparam.price");
var totalprice = parseInt(quantity) * parseInt(price);
context.setVariable("TotalPrice", totalprice);
```



## Syntax error

The error type `SyntaxError` is thrown when the JavaScript engine encounters tokens
or a token order that does not conform to the syntax of the language, or when an invalid
format input is passed to the parser APIs such as JSON/XML parse.

For example, if the invalid or malformed JSON payload is passed as the input to the `JSON.parse` API used within the JavaScript policy, then you get this error.


### Error response body


<pre>
{
    "fault": {
        "faultstring": "Execution of <var>javascript_policy_name</var> failed with error: Javascript runtime error: \"<b>SyntaxError</b>: <var>error_description</var>. (<var>javascript_source_file_name</var>:<var>line_number</var>)\"",
        "detail": {
            "errorcode": "steps.javascript.ScriptExecutionFailed"
        }
    }
}
</pre>



### Example error response body


```
{
    "fault": {
        "faultstring": "Execution of ParseJSONRequest failed with error: Javascript runtime error: \"SyntaxError: Unexpected token: <. (ParseJSONRequest.js:2)\"",
        "detail": {
            "errorcode": "steps.javascript.ScriptExecutionFailed"
        }
    }
}
```



#### Diagnosis



1.  Identify the JavaScript policy, its source file,  the line number where the error occurred, and the error description. You can find all this information in the `faultstring` element of the error response. For example, in the following `faultstring`, the JavaScript policy name is `ParseJSONRequest`, the JavaScript source file is `ParseJSONRequest.js`, the line number at which the error occurred is `2` and the error description is `Unexpected token`:

  ```
  "faultstring": "Execution of ParseJSONRequest failed with error: Javascript runtime error: \"SyntaxError: Unexpected token: <. (ParseJSONRequest.js:2)\""
  ```


1.  Examine the line number 2 in the JavaScript source file (identified in step #1 above) and check what operation is being performed. If a `JSON.parse()` function is being executed then check the input parameter passed to it. If the input parameter is not valid or is malformed JSON, then that's the cause for the error.


  Here's a sample JavaScript code that leads to this error:


  ```
  var input = context.getVariable("request.content");
  var result = JSON.parse(input);
  ```

  In this example, the request payload (`request.content`) passed to the API Proxy is used as the input to `JSON.parse()` function.

  Here's the sample API call that shows how the request was passed:


  ```
  curl -v "http://<org>-<env>.apigee.net/v1/js-demo" -H "Content-Type: application/json" -X POST -d '<city>Bangalore</city>'
  ```



  In the above request, the following XML payload is passed to the API Proxy
  `<city>Bangalore</city>`. The `JSON.parse` API expects a valid JSON to be passed
  but because an XML payload is passed instead, it fails with the below error:


  ```
  "Execution of ParseJSONRequest failed with error: Javascript runtime error: \"SyntaxError: Unexpected token: <. (ParseJSONRequest.js:2)\""
  ```



#### Resolution

Be sure to pass valid input to the parse APIs used in the JavaScript code.

To fix the issue with the sample policy discussed above, pass a valid JSON payload request as follows:


```
curl -v "http://<org>-<env>.apigee.net/v1/js-demo" -H "Content-Type: application/json" -X POST -d '{"city" : "Bangalore"}'
```



## Type error

The error type `TypeError` is thrown when:

*   An operand or argument passed to a function is incompatible with the type expected by that operator or function. 
*   A function is invoked on a null, undefined or wrong object.
*   A property is accessed from a null, undefined or wrong object.

For example, a type error is thrown:


* If you attempt to invoke the `toUpperCase()` function on a number.  This is
because the `toUpperCase()` function can be invoked only on string objects.
* If you attempt to read a property from a null or undefined object.


### Error response body


<pre>
{
    "fault": {
        "faultstring": "Execution of <var>javascript_policy_name</var> failed with error: Javascript runtime error: \"<b>TypeError</b>: <var>error_description</var>. (<var>javascript_source_file_name</var>:<var>line_number</var>)\"",
        "detail": {
            "errorcode": "steps.javascript.ScriptExecutionFailed"
        }
    }
}
</pre>


Note: The diagnosis and resolution depends on the exact error message thrown by the JavaScript policy.  A couple of examples are described below for your reference.


### Example 1: Invoking a function on the wrong object

If you attempt to invoke a function on an unsupported object, then you get this error.
For example, if you attempt to invoke the `toUpperCase()` function on a number you get
the error.  This is because the `toUpperCase()` function can be invoked only on
string objects.


#### Example error response body


```
{
    "fault": {
        "faultstring": "Execution of ConvertToUpperCase failed with error: Javascript runtime error: \"TypeError: Cannot find function toUpperCase in object 100. (ConvertToUpperCase.js:2)\"",
        "detail": {
            "errorcode": "steps.javascript.ScriptExecutionFailed"
        }
    }
}
```

#### Diagnosis



1.  Identify the JavaScript policy, its source file,  the line number where the error occurred, and the error description. You can find all this information in the `faultstring` element of the error response. For example, in the following `faultstring`, the JavaScript policy name is `ConvertToUpperCase`, the source file is `ConvertToUpperCase.js`, the line number is `2` and the error description is **<code>Cannot find function toUpperCase in object 100.</code></strong>

  ```
  "faultstring": "Execution of ConvertToUpperCase failed with error: Javascript runtime error: \"TypeError: Cannot find function toUpperCase in object 100. (ConvertToUpperCase.js:2)\""
  ```



  The error description indicates that you are invoking the function `toUpperCase()` on an object whose numeric value is 100.

1.  Examine the JavaScript source file and verify if you are invoking the function `toUpperCase()` on an object whose numeric value is 100 at line number 2 (identified in step # 1 above). If yes, then that's the cause of the error.

  Here's a sample JavaScript source file that leads to this error:

  **ConvertToUpperCase.js**

  ```
  var number = 100;
  var result = number.toUpperCase();
  ```



  In the JavaScript code shown above, the variable `number` is set with a value of 100.  Subsequently the `toUpperCase()(` function is invoked on the number object.  Since `toUpperCase()` function can be invoked only on string objects, you get the error:


  ```
  "Execution of ConvertToUpperCase failed with error: Javascript runtime error: \"TypeError: Cannot find function toUpperCase in object 100. (ConvertToUpperCase.js:2)\""
  ```


#### Resolution

Always use functions such as `toUpperCase()` on valid objects. 

To correct the example shown above, you can create a string variable and then invoke
the `toUpperCase()` function on a string:


```
var text = "Hello Apigee !";
var result = text.toUpperCase();
```



### Example 2: Cannot read property from an undefined object

If you attempt to access/read a property from an undefined object, then you get
this error. For example, this error can occur when you attempt to access data from
an object in an array, but the object is undefined. See the detailed explanation
below. 

#### Example error response body


```
{
    "fault": {
        "faultstring": "Execution of ParseJSONResponse failed with error: Javascript runtime error: \"TypeError: Cannot read property \"length\" from undefined. (ParseJSONResponse.js:7)\"",
        "detail": {
            "errorcode": "steps.javascript.ScriptExecutionFailed"
        }
    }
}
```



#### Diagnosis



1.  Identify the JavaScript policy, its source file,  the line number where the error occurred and the error description. You can find all this information in the `faultstring` element of the error response. For example, in the following `faultstring`, policy name is `ParseJSONResponse`, the source file is `ParseJSONResponse.js`, the line number is `6` and the error description is `Cannot read property "length" from undefined`.

  ```
  "faultstring": "Execution of ParseJSONResponse failed with error: Javascript runtime error: \"TypeError: Cannot read property \"length\" from undefined. (ParseJSONResponse.js:6)\""
  ```



  The error indicates that the property **length** cannot be read from an undefined object.

1.  Examine the line number in the JavaScript source file (identified in step #1 above) and verify if the object has a valid value or is undefined. You may have to read and understand the complete source file to determine how the specific object was defined or derived and determine why the object is found to be undefined. If you find that the specific object is indeed undefined and you are trying to access the property length from it, then that's the cause of the error.


  Let's look at an example to understand this issue better:


  1.  Assume that you receive the following JSON response from the backend server: 

      ```
      {
          "cars": [
             { "name":"Toyota", "count": 150 }
             { "name":"Honda", "count": 100 },
             { "name":"Ford", "count": 75 }
          ]
      }
      ```

  1.  Here's a sample JavaScript source file that parses this JSON response and
  leads to the error mentioned above:
  
      **ParseJSONResponse.js**

      ```
      // Get the JSON response
      var jsonData = context.getVariable("response.content"); 
      print (jsonData); 

      // Read the cars array
      for (var i = 0; i < jsonData.cars.length; i++) 
        {
        print("name = " + jsonData.cars[i].name);
        print("count = " + jsonData.cars[i].count);
        }
      ```

  1.  Examining the JavaScript code carefully you can see that in line number 2, the `response.content `is read/stored to the variable `jsonData` as a regular string (in quotes).

  1.  Because `jsonData` is a regular string, when you attempt to access `cars` from `jsonData` (`jsonData.cars`) will be undefined.

  1.  Subsequently, when you attempt to read the property `length` from `jsonData.cars`, which is undefined, you get the error:

      ```
      "faultstring": "Execution of ParseJSONResponse failed with error: Javascript runtime error: \"TypeError: Cannot read property \"length\" from undefined. (ParseJSONResponse.js:6)\""
      ```


#### Resolution

Ensure that you always read JSON data as a JSON object using the relevant JSON APIs.

To fix the example JavaScript described above, you can use the `JSON.parse()` function
on the `response.content` object to get it as a JSON object.  After this, you can
access the `cars` array and iterate through the array successfully.


```
// Get the JSON response
var data = context.getVariable("response.content");
var jsonData = JSON.parse(data);
print (jsonData); 

// Read the cars array
for (var i = 0; i < jsonData.cars.length; i++) 
{
    print("name = " + jsonData.cars[i].name);
    print("count = " + jsonData.cars[i].count);
}
```



## URI Error

The error type `URIError` is thrown if you use illegal characters in a URI function.
For example, if you pass a URI which has a percentage symbol to the `decodeURI` or
`decodeURIComponent` functions, then you will get this error.


### Error response body


<pre>
{
    "fault": {
        "faultstring": "Execution of <var>javascript_policy_name</var> failed with error: Javascript runtime error: \"<b>URIError</b>: <var>error_description</var>. (<var>javascript_source_file_name</var>:<var>line_number</var>)\"",
        "detail": {
            "errorcode": "steps.javascript.ScriptExecutionFailed"
        }
    }
}
</pre>



### Example error response body


```
{
    "fault": {
        "faultstring": "Execution of URIDecode failed with error: Javascript runtime error: \"URIError: Malformed URI sequence. (URIDecode.js:2)\"",
        "detail": {
            "errorcode": "steps.javascript.ScriptExecutionFailed"
        }
    }
}
```

#### Diagnosis

1.  Identify the JavaScript policy, its source file,  the line number where the
error occurred, and the error description. You can find all this information in
the faultstring element of the error response. For example, in the following
faultstring, the JavaScript policy name is `URIDecode</code` the JavaScript
source file is `URIDecode.js`, the line number is
`2` and the error description is `Malformed URI sequence`:

  ```
  "faultstring": "Execution of URIDecode failed with error: Javascript runtime error: \"URIError: Malformed URI sequence. (URIDecode.js:2)\""
  ```

  The error description indicates that a malformed URI sequence is used in line number 2 of `URIDecode.js`.

1.  Examine the JavaScript source file and verify if the argument passed to any of the URI functions contains any illegal characters. If yes, then that's the cause of error.

  Here's an example JavaScript source file that leads to this error:

  **URIDecode.js**
  
  ```
  var str = "75%-Completed";
  var decoded_str = decodeURIComponent(str);
  context.setVariable("decoded_str", decoded_str);
  ```

  In the example JavaScript code shown above, the variable `str` passed to `decodeURIComponent()` has a percentage symbol, which is considered an illegal character; therefore, you get the error:

  ```
  "Execution of URIDecode failed with error: Javascript runtime error: \"URIError: Malformed URI sequence. (URIDecode.js:2)\""
  ```

#### Resolution

Ensure that all the characters used in URI functions are legal and permitted. 

To fix the issue with the example JavaScript described above, use encode the percentage symbol. For example,  `%25`:


```
var str = "75%25-Completed";
var decoded_str = decodeURIComponent(str);
context.setVariable("decoded_str", decoded_str);
```

{% endblock %}
