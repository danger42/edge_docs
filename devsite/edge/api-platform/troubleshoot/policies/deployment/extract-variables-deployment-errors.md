{% extends "_base.html" %} {% block title %}Extract Variables policy deployment error troubleshooting{% endblock %} {% block body %}

<aside class="note">Was this troubleshooting playbook helpful? Please let us know
  by clicking <a href="" class="google-feedback"
   data-p="1636213" data-b="docsite" data-context="troubleshooting-feedback">
  Send Feedback 
</a>.</aside>


## NothingToExtract


### Error message

Deployment of the API proxy through either the Edge UI or Edge management API fails with this error message:


<pre>
Error Saving Revision [<b><i>revision_number</b></i>]
ExtractVariables [<b><i>policy_name</b></i>]: at least one of URIPath, QueryParam, Header, FormParam, XMLPayload, JSONPayload is mandatory. Revision:[<b><i>revision_number</b></i>]; APIProxy:[<b><i>proxy_name</b></i>]; Organization:[<b><i>org_name</b></i>]; Environment:[<b><i>env_name</b></i>].
</pre>


**Example error message**


```
Error Saving Revision 2
ExtractVariables EV-XML-Age: at least one of URIPath, QueryParam, Header, FormParam, XMLPayload, JSONPayload is mandatory.
```


**Example screenshot**



<img alt="" src="/api-platform/images/ev-deploy-error-3.png">



### Cause

If the Extract Variables Policy does not have any of the elements `URIPath`, `QueryParam`, `Header`, `FormParam`, `XMLPayload`, or `JSONPayload`, the deployment of the API Proxy fails, because there's nothing to extract.

As indicated in the error message, the Extract Variables policy must have at least one of these elements included within the policy: `URIPath`, `QueryParam`, `Header`, `FormParam`, `XMLPayload`, or `JSONPayload`.


### Diagnosis

Examine the Extract Variables policy named in the error message. If the policy does not have even one of the following elements:  `URIPath`, `QueryParam`, `Header`, `FormParam`, `XMLPayload`, or `JSONPayload`, then that's the cause of the error.  For example, the following Extract Variables policy does not have any of the above mentioned elements:


```
<ExtractVariables async="false" continueOnError="false" enabled="true" name="EV-XML-Age">
    <DisplayName>EV-XML-Age</DisplayName>
    <Properties/>
</ExtractVariables>
```


Since none of the mandatory elements are present in the Extract Variables policy, the deployment of the API proxy fails.


### Resolution

Ensure that the Extract Variables policy has at least one of these mandatory elements: `URIPath`, `QueryParam`, `Header`, `FormParam`, `XMLPayload`, `JSONPayload`. For example:


```
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<ExtractVariables async="false" continueOnError="false" enabled="true" name="EV-XML-Age">
  <DisplayName>EV-XML-Age</DisplayName>
  <Properties/>
  <Source>request</Source>
  <XMLPayload>
    <Namespaces/>
      <Variable name="age" type="integer">
          <XPath>/age</XPath>
      </Variable>
  </XMLPayload>
</ExtractVariables>
```



## NONEmptyPrefixMappedToEmptyURI





### Error message

Deployment of the API proxy through either the Edge UI or Edge management API fails with this error message:


<pre>
Error Saving Revision [<b><i>revision_number</b></i>]
ExtractVariables [<b><i>policy_name</b></i>]: Non-empty prefix [<b><i>prefix_name</b></i>] cannot be mapped to empty uri.
</pre>


**Example error message**


```
Error Saving Revision 3
ExtractVariables EV-XML-Age: Non-empty prefix apigee cannot be mapped to empty uri.
```


**Example screenshot**


<img alt="" src="/api-platform/images/ev-deploy-error-5.png">



### Cause

This error occurs if the Extract Variables Policy has a prefix defined in the `<Namespace>` element under the `<XMLPayload>` element, but no URI is defined.


### Diagnosis



1.  Identify the Extract Variables policy where the error occurred and the name of the prefix. You can find both of these items in the error message. For example, in the following error , the policy name is `EV-XML-Age` and the prefix is `apigee`:

    ```
    ExtractVariables EV-XML-Age: Non-empty prefix apigee cannot be mapped to empty uri.
    ```


1.  In the failed Extract Variables policy XML, verify that the name of the prefix set in the `<Namespace>` element under the `<XMLPayload>` element matches the prefix name identified in the error message (step #1 above). For example, the following policy specifies a prefix named `apigee` in the `<Namespace>` element , which matches what's in the error message:

    ```
    <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
    <ExtractVariables async="false" continueOnError="false" enabled="true" name="EV-XML-Age">
        <DisplayName>EV-XML-Age</DisplayName>
        <Source clearPayload="false">request</Source>
        <XMLPayload stopPayloadProcessing="false">
          <Namespaces>
            <Namespace prefix="apigee"></Namespace>
            <Namespace prefix="gmail">http://mail.google.com</Namespace>
          </Namespaces>
        <Variable name="legName" type="string">
        <XPath>/apigee:Directions/apigee:route/apigee:leg/apigee:name</Xpath>
            </Variable>
        </XMLPayload>
    </ExtractVariables>
    ```


1.  Validate if the `<Namespace>` element with the specific prefix identified in step #2 has a valid URI.  If the URI is missing, then that is the cause of the error.

    In the example Extract Variables policy shown above, notice that there's no URI corresponding to the  `<Namespace>` element with the prefix `apigee`; therefore, you get the error:


    ```
    ExtractVariables EV-XML-Age: Non-empty prefix apigee cannot be mapped to empty uri.

    ```



### Resolution

Ensure that all the `<Namespace>` elements defined with a prefix have a corresponding URI in the Extract Variables policy. For example:


```
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<ExtractVariables async="false" continueOnError="false" enabled="true" name="EV-XML-Age">
    <DisplayName>EV-XML-Age</DisplayName>
    <Source clearPayload="false">request</Source>  
    <XMLPayload stopPayloadProcessing="false">
    <Namespaces>
        <Namespace prefix="apigee">http://www.apigee.com</Namespace>
        <Namespace prefix="gmail">http://mail.google.com</Namespace>
     </Namespaces>
    <Variable name="legName" type="string">
        <XPath>/apigee:Directions/apigee:route/apigee:leg/apigee:name</Xpath>
    </Variable>
    </XMLPayload>
</ExtractVariables>
```





## DuplicatePrefix


### Error message

Deployment of the API proxy through either the Edge UI or Edge management API fails with this error message:


<pre>
Error Saving Revision [<b><i>revision_number</b></i>]
ExtractVariables [<b><i>policy_name</b></i>]: Duplicate prefix [<b><i>prefix_name</b></i>].
</pre>


**Example error message**


```
Error Saving Revision 2
ExtractVariables EV-XML-Age: Duplicate prefix apigee.
```


**Example screenshot**


<img alt="" src="/api-platform/images/ev-deploy-error-7.png">



### Cause

This error occurs, if the Extract Variables Policy has the same prefix defined more than once in the `<Namespace>` element under the `<XMLPayload>` element.

For example, this error occurs since the prefix `apigee` is defined twice as shown below: 


```
<Namespace prefix="apigee">http://www.apigee.com</Namespace>
<Namespace prefix="apigee">http://www.apigee.com</Namespace>
```



### Diagnosis



1.  Identify the Extract Variables policy where the error occurred and the name of the prefix. You can find both of these items in the error message. For example, in the following error, the policy name is `EV-XML-Age` and the prefix is `apigee`:

    ```
    ExtractVariables EV-XML-Age: Duplicate prefix apigee.
    ```


1.  In the failed Extract Variables policy XML, verify that the name of the prefix set in the `<Namespace>` element under the `<XMLPayload>` element matches the prefix name identified in the error message (step #1 above). For example, the following policy specifies a prefix named `apigee` in the `<Namespace>` element , which matches what's in the error message:

    ```
    <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
    <ExtractVariables async="false" continueOnError="false" enabled="true" name="EV-XML-Age">
        <DisplayName>EV-XML-Age</DisplayName>
        <Source clearPayload="false">request</Source>  
        <XMLPayload stopPayloadProcessing="false">
          <Namespaces>
              <Namespace prefix="gmail">http://mail.google.com</Namespace>
              <Namespace prefix="apigee">http://www.apigee.com</Namespace>
              <Namespace prefix="apigee">http://www.apigee.com</Namespace>
          </Namespaces>
          <Variable name="legName" type="string
              <XPath>/apigee:Directions/apigee:route/apigee:leg/apigee:name</XPath>
          </Variable>
        </XMLPayload>
      </ExtractVariables>
    ```


1.  Determine if the `<Namespace>` element with the specific prefix, identified in step #2 has been defined more than once.  If it is defined more than once, then that is the cause of the error.

    In the example Extract Variables policy shown above, notice that the  `<Namespace>` element with the prefix `apigee` has been defined twice; therefore, you get the error:


    ```
    ExtractVariables EV-XML-Age: Duplicate prefix apigee.

    ```



### Resolution

Ensure that there is only one definition for each prefix in the `<Namespace>` elements in the Extract Variables policy. For example:


```
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<ExtractVariables async="false" continueOnError="false" enabled="true" name="EV-XML-Age">
    <DisplayName>EV-XML-Age</DisplayName>
    <Source clearPayload="false">request</Source>  
    <XMLPayload stopPayloadProcessing="false">
      <Namespaces>
          <Namespace prefix="apigee">http://www.apigee.com</Namespace>
          <Namespace prefix="gmail">http://mail.google.com</Namespace>
      </Namespaces>
      <Variable name="legName" type="string">
          <XPath>/apigee:Directions/apigee:route/apigee:leg/apigee:name</Xpath>
      </Variable>
    </XMLPayload>
</ExtractVariables>
```



## NoXPathsToEvaluate





### Error message

Deployment of the API proxy through either the Edge UI or Edge management API fails with this error message:


<pre>
Error Saving Revision [<b><i>revision_number</b></i>]
ExtractVariables [<b><i>policy_name</b></i>]:no xpaths to evaluate in variable name.
</pre>


**Example error message**


```
Error Saving Revision 4
ExtractVariables Extract-Variables-1: no xpaths to evaluate in variable name.
```


**Example screenshot**


<img alt="" src="/api-platform/images/ev-deploy-error-8.png">



### Cause

If the Extract Variables Policy does not have the `<XPath>` element within the `<XMLPayload>` element, then the deployment of the API proxy fails with the error shown above. 

<aside class="note">If you want to extract variables from an XML Payload, then an <code>XPath</code> child element must be specified.</aside>


### Diagnosis



1.  Identify the failed Extract Variables policy from the error message. For example, in the following error, the policy name is `Extract-Variables-1`:

    ```
    ExtractVariables Extract-Variables-1: no xpaths to evaluate in variable name.
    ```


1.  In the failed Extract Variables policy XML, check if there's an `<XMLPayload>` element without the `<XPath>` child element. If so, then that's the cause of the error.

    For example, here's an Extract Variables policy that has an `<XMLPayload>` element:


    ```
    <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
    <ExtractVariables async="false" continueOnError="false" enabled="true" name="Extract-Variables-1">
        <DisplayName>Extract Variables-1</DisplayName>
        <Properties/>
        <Source clearPayload="false">request</Source>
        <VariablePrefix>apigee</VariablePrefix>
        <XMLPayload stopPayloadProcessing="false">
            <Namespaces/>
            <Variable name="HostReachable" type="boolean">
            </Variable>
        </XMLPayload>
    </ExtractVariables>
    ```



    Because there is no `<XPath>` element present, the deployment of the API Proxy fails.



### Resolution

Ensure that the Extract Variables policy has an `<XPath>` element defined under the `<XMLPayload>` element. For example:


```
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<ExtractVariables async="false" continueOnError="false" enabled="true" name="Extract-Variables-1">
    <DisplayName>Extract Variables-1</DisplayName>
    <Properties/>
    <Source clearPayload="false">request</Source>
    <VariablePrefix>apigee</VariablePrefix>
    <XMLPayload stopPayloadProcessing="false">
        <Namespaces/>
        <Variable name="HostReachable" type="boolean">
            <XPath>/isReachable</XPath>
        </Variable>
    </XMLPayload>
</ExtractVariables>
```





## EmptyXPathExpression



### Error message

Deployment of the API proxy through either the Edge UI or Edge management API fails with this error message:


<pre>
Error Saving Revision [<b><i>revision_number</b></i>]
ExtractVariables [<b><i>policy_name</b></i>]: XPath expression is empty in variable name.
</pre>


**Example error message**


```
Error Saving Revision 6
ExtractVariables Extract-Variables-1: XPath expression is empty in variable name.
```


**Example screenshot**


<img alt="" src="/api-platform/images/ev-deploy-error-2.png">



### Cause

If the Extract Variables Policy has an empty `<XPath>` expression within the `<XMLPayload>` element, then the deployment of the API proxy fails.


### Diagnosis



1.  Identify the failed Extract Variables policy from the error message. For example, in the following error, the policy name is `Extract-Variables-1`:

    ```
    ExtractVariables Extract-Variables-1: no xpaths to evaluate in variable name.
    ```


1.  In the failed Extract Variables policy XML, determine if there's an `<XMLPayload>` element with an empty `<XPath>` child element. If so, then that's the cause of the error.

    For example, here's an Extract Variables policy that has an `<XMLPayload>` element:


    ```
    <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
    <ExtractVariables async="false" continueOnError="false" enabled="true" name="Extract-Variables-1">
        <DisplayName>Extract Variables-1</DisplayName>
        <Properties/>
        <Source clearPayload="false">request</Source>
        <VariablePrefix>apigee</VariablePrefix>
        <XMLPayload stopPayloadProcessing="false">
            <Namespaces/>
            <Variable name="address" type="string">
                <XPath></XPath>
            </Variable>
        </XMLPayload>
    </ExtractVariables>
    ```



    Because there is an empty  `<XPath>` element within the `<XMLPayload>` element, the deployment of the API Proxy fails.



### Resolution

Ensure that the Extract Variables policy has a non-empty and valid `<XPath>` element defined under the `<XMLPayload>` element. For example: 


```
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<ExtractVariables async="false" continueOnError="false" enabled="true" name="Extract-Variables-1">
    <DisplayName>Extract Variables-1</DisplayName>
    <Properties/>
    <Source clearPayload="false">request</Source>
    <VariablePrefix>apigee</VariablePrefix>
    <XMLPayload stopPayloadProcessing="false">
        <Namespaces/>
        <Variable name="address" type="string">
            <XPath>/address</XPath>
        </Variable>
    </XMLPayload>
</ExtractVariables>
```



## NoJSONPathsToEvaluate





### Error message

Deployment of the API proxy through either the Edge UI or Edge management API fails with this error message:


<pre>
Error Saving Revision [<b><i>revision_number</b></i>]
ExtractVariables [<b><i>policy_name</b></i>]:no jsonpaths to evaluate in variable name.
</pre>


**Example error message**


```
Error Saving Revision 6
ExtractVariables Extract-Variables-1: no jsonpaths to evaluate in variable name.
```


**Example screenshot**


<img alt="" src="/api-platform/images/ev-deploy-error-4.png">



### Cause

If the Extract Variables Policy does not have the `<JSONPath>` element within the `<JSONPayload>` element, then the deployment of the API proxy fails with the error shown above. 

<aside class="note">If you want to extract variables from a JSON Payload, then the <code>JSONPath</code> child element is required.</aside>


### Diagnosis



1.  Identify the failed Extract Variables policy from the error message. For example, in the following error, the policy name is `Extract-Variables-1`:

    ```
    ExtractVariables Extract-Variables-1: no jsonpaths to evaluate in variable name.
    ```


1.  In the failed Extract Variables policy XML, determine if there's a `<JSONPayload>` element without the required `<JSONPath>` child element. If so, then that's the cause of the error.

    For example, here's an Extract Variables policy that has `<JSONPayload>` element:


    ```
    <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
    <ExtractVariables async="false" continueOnError="false" enabled="true" name="Extract-Variables-1">
        <DisplayName>Extract Variables-1</DisplayName>
        <Properties/>
        <JSONPayload>
            <Variable name="title">
            </Variable>
        </JSONPayload>
        <Source clearPayload="false">request</Source>
        <VariablePrefix>apigee</VariablePrefix>
    </ExtractVariables>
    ```



    Because there is no `<JSONPath>` child element defined under the `<JSONPayload>` element, the deployment of the API Proxy fails.



### Resolution

Ensure that the Extract Variables policy has a  `<JSONPath>` element defined under the `<JSONPayload>` element. For example:


```
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<ExtractVariables async="false" continueOnError="false" enabled="true" name="Extract-Variables-1">
    <DisplayName>Extract Variables-1</DisplayName>
    <Properties/>
    <JSONPayload>
        <Variable name="title">
            <JSONPath>$.book.title</JSONPath>
        </Variable>
    </JSONPayload>
    <Source clearPayload="false">request</Source>
    <VariablePrefix>apigee</VariablePrefix>
</ExtractVariables>
```





## EmptyJSONPathExpression



### Error message

Deployment of the API proxy through either the Edge UI or Edge management API fails with this error message:


<pre>
Error Saving Revision [<b><i>revision_number</b></i>]
ExtractVariables [<b><i>policy_name</b></i>]: JSONPath expression is empty in variable name.
</pre>


**Example error message**


```
Error Saving Revision 6
ExtractVariables Extract-Variables-1: JSONPath expression is empty in variable name.
```


**Example screenshot**


<img alt="" src="/api-platform/images/ev-deploy-error-1.png">



### Cause

If the Extract Variables Policy has an empty `<JSONPath>` expression within the `<JSONPayload>` element, then the deployment of the API proxy fails.


### Diagnosis



1.  Identify the failed Extract Variables policy from the error message. For example, in the following error, the policy name is `Extract-Variables-1`:

    ```
    ExtractVariables Extract-Variables-1: JSONPath expression is empty in variable name.
    ```


1.  In the failed Extract Variables policy XML, verify if there's a `<JSONPayload>` element with an empty `<JSONPath>` child element. If so, then that's the cause of the error.

    For example, here's an Extract Variables policy that has a `<JSONPayload>` element:

  ```
      <ExtractVariables async="false" continueOnError="false" enabled="true" name="Extract-Variables-1">
          <DisplayName>Extract Variables-1</DisplayName>
          <Properties/>
          <JSONPayload>
              <Variable name="account_number" type="integer">
                  <JSONPath></JSONPath>
              </Variable>
          </JSONPayload>
          <Source clearPayload="false">request</Source>
          <VariablePrefix>apigee</VariablePrefix>
      </ExtractVariables>
  ```



  Because there is an empty  `<JSONPath>` element within the `<JSONPayload>` element, the deployment of the API Proxy fails. 


### Resolution

Ensure that the Extract Variables policy has a non-empty and valid `<JSONPath>` element defined under the `<JSONPayload>` element. For example: 


```
<ExtractVariables async="false" continueOnError="false" enabled="true" name="Extract-Variables-1">
    <DisplayName>Extract Variables-1</DisplayName>
    <Properties/>
    <JSONPayload>
        <Variable name="account_number" type="integer">
           <JSONPath>$.account.number</JSONPath>
        </Variable>
    </JSONPayload>
    <Source clearPayload="false">request</Source>
    <VariablePrefix>apigee</VariablePrefix>
</ExtractVariables>
```





## MissingName



### Error message

Deployment of the API proxy through either the Edge UI or Edge management API fails with this error message:


<pre>
Error Saving Revision [<b><i>revision_number</b></i>]
Error occurred while validation of bean [<b><i>policy_name</b></i>].xml Reason: - Required attribute name is missing in [<b><i>element_name</b></i>]
</pre>


**Example error message**


```
Error Saving Revision 6
Error occurred while validation of bean Extract-Variables-1.xml. Reason: - Required attribute name is missing in Variable
```


**Example screenshot**


<img alt="" src="/api-platform/images/ev-deploy-error-12.png">



### Cause

If the Extract Variables Policy does not have the `name` attribute in any of the policy elements like `QueryParam`, `Header`, `FormParam` or  `Variable`, where it is required, then the deployment of the API proxy fails.

For example, this error occurs if the `name` attribute is missing in a `Variable` element.


### Diagnosis



1.  Identify the name of the Extract Variables policy where the error occurred and the element where the `name` attribute is missing. You can find these items in the error message. For example, in the following error, the policy name is `Extract-Variables-1` and the element name is `Variable`:

    ```
    Error occurred while validation of bean Extract-Variables-1.xml. Reason: - Required attribute name is missing in Variable
    ```


1.  In the failed Extract Variables policy XML, check that all the elements with the name determined from step #1 above have a `name` attribute. If there's any element where the name attribute is missing, then that is the cause of the error.

    For example, the following Extract Variables policy has the `<Variable>` element defined:


    ```
    <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
    <ExtractVariables async="false" continueOnError="false" enabled="true" name="Extract-Variables-1">
        <DisplayName>Extract Variables-1</DisplayName>
        <Properties/>
        <Variable type="boolean">
            <Pattern>{isAccountActive}</Pattern>
        </Variable>
    </ExtractVariables>
    ```



    Notice that the `name` attribute is missing in the `Variable` element, so the deployment of the API Proxy fails.



### Resolution

Ensure that the Extract Variables Policy contains the required `name` attribute, for the elements `QueryParam`, `Header`, `FormParam` , or `Variable`. For example:


```
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<ExtractVariables async="false" continueOnError="false" enabled="true" name="Extract-Variables-1">
    <DisplayName>Extract Variables-1</DisplayName>
    <Properties/>
    <Variable name="account_active" type="boolean">
        <Pattern>{isAccountActive}</Pattern>
    </Variable>
</ExtractVariables>
```





## PatternWithoutVariable


### Error message

Deployment of the API proxy through either the Edge UI or Edge management API fails with this error message:


<pre>
Error Saving Revision [<b><i>revision_number</b></i>]
Pattern [pattern] should have atleast one variable in ExtractVariables stepDefinition [<b><i>policy_name</b></i>]
</pre>


**Example error message**


```
Error Saving Revision 7
Pattern /a/b/ should have atleast one variable in ExtractVariables stepDefinition ExtractVariables-1.
```


**Example screenshot**


<img alt="" src="/api-platform/images/ev-deploy-error-6.png">



### Cause

If the Extract Variables Policy does not have a variable specified within the `<Pattern>` element, then the deployment of the API proxy fails. The `<Pattern>` element requires the name of the variable in which extracted data will be stored.


### Diagnosis 



1.  Identify the name of the Extract Variables policy where the error occurred and the pattern for which the Variable is missing. You can find these items from the error message. For example, in the following error, the policy name is `Extract-Variables-1` and the element name is `/a/b`:

    ```
    Pattern /a/b/ should have atleast one variable in ExtractVariables stepDefinition ExtractVariables-1.
    ```


1.  In the failed Extract Variables policy XML, verify that the pattern set in the `<Pattern>` element matches the pattern identified in the error message (step #1 above). For example, the following policy specifies a pattern `/a/b`, which matches what's in the error message:

    For example, here's an Extract Variables policy that has three `<Pattern>` elements:


    ```
    <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
    <ExtractVariables name="ExtractVariables-1">
       <Source>request</Source>
       <URIPath>
          <Pattern ignoreCase="true">/a/{pathSeg}</Pattern>
          <Pattern ignoreCase="true">/a/b/</Pattern>
          <Pattern ignoreCase="true">/a/b/c/{pathSeg}</Pattern>
       </URIPath>
       <VariablePrefix>urirequest</VariablePrefix>
    </ExtractVariables>
    ```


1.  Check if the `<Pattern>` element identified in step #2 does not have a variable. Variables
must be enclosed in curly braces. If the pattern does not have a variable, then
that is the cause of the error.

    The `<Pattern>` element having the pattern **/a/b** does not include a variable; therefore, the deployment of the API Proxy fails


    ```
    <Pattern ignoreCase="true">/a/b/</Pattern>

    ```



### Resolution

Ensure any `<Pattern>` elements include a variable (a name enclosed in curly
braces).  For example:


```
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<ExtractVariables name="ExtractVariables-1">
   <Source>request</Source>
   <URIPath>
      <Pattern ignoreCase="true">/a/{pathSeg}</Pattern>
      <Pattern ignoreCase="true">/a/b/{pathSeg}</Pattern>
      <Pattern ignoreCase="true">/a/b/c/{pathSeg}</Pattern>
   </URIPath>
   <VariablePrefix>urirequest</VariablePrefix>
</ExtractVariables>
```





## CannotBeConvertedToNodeset


### Error message

Deployment of the API proxy through either the Edge UI or Edge management API fails with this error message:


<pre>
Error Deploying Revision [<b><i>revision_number</b></i>]
ExtractVariables [<b><i>policy_name</b></i>]: Result of xpath [<b><i>policy_name</b></i>] cannot be converted to nodeset. Context Revision:[<b><i>revision_number</b></i>]; APIProxy:[<b><i>proxy_name</b></i>]; Organization:[<b><i>org_name</b></i>]; Environment:[<b><i>env_name</b></i>].
</pre>


**Example error message**


```
Error Deploying Revision 4 to test
ExtractVariables Extract-Variables-1: Result of xpath 123 cannot be converted to nodeset. Context Revision:4;APIProxy:EV-XML;Organization:aprabhashankar-eval;Environment:test.
```


**Example screenshot**


<img alt="" src="/api-platform/images/ev-deploy-error-9.png">


### Cause

If the Extract Variables Policy has an `<XPath>` expression where the `<Variable>` type is defined as [nodeset](https://docs.microsoft.com/en-us/dotnet/standard/data/xml/node-sets-in-transformations), but the expression cannot be converted to nodeset, then the deployment of the API proxy fails. 


### Diagnosis



1.  Identify the Extract Variables policy where the error occurred and the XPath that cannot be converted to nodeset. You can find both of these items in the error message. For example, in the following error , the policy name is `Extract-Variables-1` and the XPath is `123`.

    ```
    ExtractVariables Extract-Variables-1: Result of xpath 123 cannot be converted to nodeset. Context Revision:4;APIProxy:EV-XML;Organization:aprabhashankar-eval;Environment:test.
    ```


1.  In the failed Extract Variables policy XML, verify that the XPath set in the `<XPath>` element matches the XPath identified in the error message (step #1 above). For example, the following policy specifies the <XPath> as `123`, which matches what's in the error message:

    ```
    <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
    <ExtractVariables async="false" continueOnError="false" enabled="true" name="Extract-Variables-1">
        <DisplayName>Extract Variables-1</DisplayName>
        <Properties/>
        <Source clearPayload="false">request</Source>
        <VariablePrefix>visitor</VariablePrefix>
        <XMLPayload stopPayloadProcessing="false">
            <Namespaces/>
            <Variable name="age" type="nodeset">
                <XPath>123</XPath>
            </Variable>
        </XMLPayload>
    </ExtractVariables>
    ```


1.  Examine the type of the `<Variable>` element corresponding to the `<XPath>` identified in step #2 above. If the `<Variable>` type is `nodeset`, then that's the cause of the error.

    Notice that the `<XPath>` expression is `123` in the example Extract Variables policy. 


    ```
    <XPath>123</XPath>
    ```



    The expression `123` cannot be converted to nodeset. So, the deployment of the API Proxy fails.



### Resolution

Ensure that the `<XPath>` expressions used in the Extract Variables policy can be converted to nodeset if the `<Variable>` type is defined as nodeset. For example: 


```
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<ExtractVariables async="false" continueOnError="false" enabled="true" name="Extract-Variables-1">
    <DisplayName>Extract Variables-1</DisplayName>
    <Properties/>
    <Source clearPayload="false">request</Source>
    <VariablePrefix>visitor</VariablePrefix>
    <XMLPayload stopPayloadProcessing="false">
        <Namespaces/>
        <Variable name="age" type="nodeset">
            <XPath>/visitor/age</XPath>
        </Variable>
    </XMLPayload>
</ExtractVariables>
```





## InvalidPattern


### Error message

Deployment of the API proxy through either the Edge UI or Edge management API fails with this error message:


```
Error Deploying Revision [revision_number]
Pattern [pattern] is invalid in ExtractVariables stepDefinition [policy_name]
```


**Example error message**


```
Error Deploying Revision 18 to test
Pattern {*} is invalid in ExtractVariables stepDefinition ExtractVariables-2.
```


**Example screenshot**


<img alt="" src="/api-platform/images/ev-deploy-error-10.png">


### Cause

If the `<Pattern>` element definition is invalid in any of the elements like `URIPath`, `QueryParam`, `Header`, `FormParam`, `XMLPayload` or `JSONPayload` within the Extract Variables Policy, then the deployment of the API proxy fails.

<aside class="note">The <code>Pattern</code> element is considered invalid if it
  doesn't include a variable. For more information about pattern matching, see
  <a href="/api-platform/reference/policies/extract-variables-policy#about-matching-variable-creation">About matching and variable creation</a>.</aside>


### Diagnosis



1.  Identify the failing Extract Variables policy from the error message. For example, in the following error, the policy name is `Extract-Variables-2`:

    ```
    Pattern {*} is invalid in ExtractVariables stepDefinition ExtractVariables-2.
    ```


1.  In the failed Extract Variables policy XML, check if any of these elements are present and include a `<Pattern>` element.: `URIPath`, `QueryParam`, `Header`, `FormParam`, `XMLPayload` or `JSONPayload`.

    For example, here's an example Extract Variables policy with a `<Pattern>` element within `<QueryParam>` element:


    ```
    <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
    <ExtractVariables name="ExtractVariables-2">
        <DisplayName>ExtractVariables-2</DisplayName>
        <Source>request</Source>
        <QueryParam name="code">
            <Pattern ignoreCase="true">{*}</Pattern>
        </QueryParam>
        <VariablePrefix>queryinfo</VariablePrefix>
    </ExtractVariables>
    ```


1.  Examine the definition of each  of the `<Pattern>` elements. If there's any `<Pattern>` element with an invalid definition, then that is the cause of the error.

    In the example Extract Variables policy shown above, notice that the `<Pattern>` definition within the `<QueryParam>` element is invalid; therefore, the deployment of the API Proxy fails. See also
  [About matching and variable creation](/api-platform/reference/policies/extract-variables-policy#about-matching-variable-creation)


    ```
    <Pattern ignoreCase="true">{*}</Pattern>

    ```



### Resolution

Ensure that the `<Pattern>` element definition within the elements `URIPath`, `QueryParam`, `Header`, `FormParam`, `XMLPayload` or `JSONPayload` in the Extract Variables policy is valid. For example:


```
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<ExtractVariables name="ExtractVariables-2">
    <DisplayName>Extract a value from a query parameter</DisplayName>
    <Source>request</Source>
    <QueryParam name="code">
        <Pattern ignoreCase="true">{code}</Pattern>
    </QueryParam>
    <VariablePrefix>queryinfo</VariablePrefix>
</ExtractVariables>
```

See also [About matching and variable creation](/api-platform/reference/policies/extract-variables-policy#about-matching-variable-creation)


## XPathCompilationFailed



### Error message

Deployment of the API proxy through either the Edge UI or Edge management API fails
with this error message:


<pre>
Error Deploying Revision [<b><i>revision_number</b></i>]
ExtractVariables [<b><i>policy_name</b></i>]: Failed to compile xpath [<b><i>xpath</b></i>] Context Revision:[<b><i>revision_number</b></i>]; APIProxy:[<b><i>proxy_name</b></i>]; Organization:[<b><i>org_name</b></i>]; Environment:[<b><i>env_name</b></i>].
</pre>


**Example error message**


```
Error Deploying Revision 1 to test
ExtractVariables EV-XML-Age: Failed to compile xpath /apigee:Directions/apigee:route/apigee:leg/apigee:name. Context Revision:1;APIProxy:EV-XML;Organization:aprabhashankar-eval;Environment:test.
```


**Example screenshot**

<img alt="" src="/api-platform/images/ev-deploy-error-13.png">



### Cause

If the prefix or the value used in the `<XPath>` element is not part of any of the declared namespaces in the Extract Variables Policy, then the deployment of the API proxy fails.

You can find more information about namespaces, XPath and prefix in [XML Namespaces and How They Affect XPath and XSLT](https://msdn.microsoft.com/en-us/library/ms950779.aspx).


### Diagnosis



1.  Identify the Extract Variables policy where the error occurred and the XPath that could not be compiled. You can find both of these items in the error message. For example, in the following error , the policy name is `EV-XML-Age` and the xpath is `/apigee:Directions/apigee:route/apigee:leg/apigee:name`.

    ```
    ExtractVariables EV-XML-Age: Failed to compile xpath /apigee:Directions/apigee:route/apigee:leg/apigee:name. Context Revision:1;APIProxy:EV-XML;Organization:aprabhashankar-eval;Environment:test.
    ```


1.  In the failed Extract Variables policy XML, verify that the XPath set in the `<XPath>` element matches the XPath identified in the error message (step #1 above). For example, the following policy specifies the <XPath> as `/apigee:Directions/apigee:route/apigee:leg/apigee:name`, which matches what's in the error message:

    ```
    <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
    <ExtractVariables async="false" continueOnError="false" enabled="true" name="EV-XML-Age">
      <DisplayName>EV-XML-Age</DisplayName>
      <Source clearPayload="false">request</Source>  
      <XMLPayload stopPayloadProcessing="false">
        <Namespaces>
            <Namespace prefix="gmail">http://mail.google.com</Namespace>
        </Namespaces>
        <Variable name="legName" type="string">
        <XPath>/apigee:Directions/apigee:route/apigee:leg/apigee:name</XPath>
            </Variable>
      </XMLPayload>
    </ExtractVariables>
    ```


1.  Examine the `<Namespaces>` and `<XPath>` elements in the Extract Variables policy. If the specific `<XPath>` indicated in the error message uses a prefix or value that is not part of the namespaces declared in the Extract Variables policy, then that is the cause of the error.

    Notice that the specific `<XPath>` uses the prefix `apigee` in the example Extract Variables policy.


    ```
    <XPath>/apigee:Directions/apigee:route/apigee:leg/apigee:name</XPath>
    ```



    However, the prefix `apigee` is not defined in any of the `<Namespace>` elements; therefore, the compilation of `<XPath>` fails leading to deployment failure.



### Resolution

Ensure all the namespaces that are being used in `<XPath>` elements are declared in the Extract Variables Policy. For example:


```
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<ExtractVariables async="false" continueOnError="false" enabled="true" name="EV-XML-Age">
  <DisplayName>EV-XML-Age</DisplayName>
  <Source clearPayload="false">request</Source>  
  <XMLPayload stopPayloadProcessing="false">
    <Namespaces>
        <Namespace prefix="apigee">http://www.apigee.com</Namespace>
        <Namespace prefix="gmail">http://mail.google.com</Namespace>
    </Namespaces>
    <Variable name="legName" type="string">
        <XPath>/apigee:Directions/apigee:route/apigee:leg/apigee:name</XPath>
    </Variable>
  </XMLPayload>
</ExtractVariables>
```

{% endblock %}
