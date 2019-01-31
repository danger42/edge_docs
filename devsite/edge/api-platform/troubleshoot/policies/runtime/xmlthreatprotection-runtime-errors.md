{% extends "_base.html" %} {% block title %}XMLThreatProtection policy runtime error troubleshooting{% endblock %} {% block body %}

<aside class="note">Was this troubleshooting playbook helpful? Please let us know
  by clicking <a href="" class="google-feedback"
   data-p="1636213" data-b="docsite" data-context="troubleshooting-feedback">
  Send Feedback 
</a>.</aside>

## ExecutionFailed


### Error Code


```
steps.xmlthreatprotection.ExecutionFailed
```


### Error response body


<pre class="prettyprint">
{
    "fault": {
        "faultstring": "XMLThreatProtection stepDefinition <var>policy_name</var>:
        Execution failed. Reason: <var>error_description</var> at line <var>line_num</var>
        (possibly around char <var>char_num</var>)",
        "detail": {
            "errorcode": "steps.xmlthreatprotection.ExecutionFailed"
        }
    }
}
</pre>


### Example Error response body

```
{
    "fault": {
        "faultstring": "XMLThreatProtection stepDefinition XML-Threat-Protection-1:
        Execution failed. reason: Premature end of document while parsing at line 5(possibly  around char 0)",
        "detail": {
            "errorcode": "steps.xmlthreatprotection.ExecutionFailed"
        }
    }
}
```


## Types of Errors and possible causes

The XMLThreatProtection policy can throw many different types of ExecutionFailed errors. The table below lists the different types of errors and their possible causes:


<table>
  <tr>
   <td>Error
   </td>
   <td>Cause
   </td>
  </tr>
  <tr>
   <td>

<a href="#elementnameexceeded">ElementNameExceeded</a>
   </td>
   <td>The maximum string length allowed in an XML tag is exceeded.
   </td>
  </tr>
  <tr>
   <td>

<a href="#childcountexceeded">ChildCountExceeded</a>
   </td>
   <td>The maximum number of child elements allowed in an XML payload is exceeded.
   </td>
  </tr>
  <tr>
   <td>


<a href="#nodedepthexceeded">NodeDepthExceeded</a>
   </td>
   <td>The maximum depth of XML elements allowed in an XML payload is exceeded.
   </td>
  </tr>
  <tr>
   <td>

<a href="#attributecountexceeded">AttributeCountExceeded</a>
   </td>
   <td>The maximum number of attributes in a single element is exceeded.
   </td>
  </tr>
  <tr>
   <td>

<a href="#attributenameexceeded">AttributeNameExceeded</a>
   </td>
   <td>The maximum length allowed for an attribute name is exceeded.
   </td>
  </tr>
  <tr>
   <td>
<a href="#attributevalueexceeded">AttributeValueExceeded</a>
   </td>
   <td>The maximum length allowed for an attribute value is exceeded.
   </td>
  </tr>
  <tr>
   <td>

<a href="#textexceeded">TextExceeded</a>
   </td>
   <td>The maximum length allowed for text is exceeded.
   </td>
  </tr>
  <tr>
   <td>

<a href="#commentexceeded">CommentExceeded</a>
   </td>
   <td>The maximum length allowed for a comment is exceeded.
   </td>
  </tr>
  <tr>
   <td>

<a href="#pidataexceeded">PIDataExceeded</a>
   </td>
   <td>The allowed processing instruction data length is exceeded.
   </td>
  </tr>
  <tr>
   <td>

<a href="#pitargetexceeded">PITargetExceeded</a>
   </td>
   <td>The process instruction name length is exceeded.
   </td>
  </tr>
  <tr>
   <td>

<a href="#nsuriexceeded">NSURIExceeded</a>
   </td>
   <td>The namespace URL length is exceeded.
   </td>
  </tr>
  <tr>
   <td>

<a href="#nsprefixexceeded">NSPrefixExceeded</a>
   </td>
   <td>The namespace prefix length is exceeded.
   </td>
  </tr>
  <tr>
   <td>

<a href="#nscountexceeded">NSCountExceeded</a>
   </td>
   <td>The number of name spaces used in a single element is exceeded.
   </td>
  </tr>
  <tr>
   <td>

<a href="#invalidxmlpayload">Invalid XML Payload</a>
   </td>
   <td>The input XML Payload is invalid.
   </td>
  </tr>
</table>

## ElementNameExceeded

### Error Code

```
steps.xmlthreatprotection.ExecutionFailed
```

### Error response body

Runtime traffic returns a 500 response code with the following error:

<pre class="prettyprint">
{
    "fault": {
        "faultstring": "XMLThreatProtection stepDefinition <var>policy_name</var>:
        Execution failed. reason: XMLThreatProtection stepDefinition <var>policy_name</var>:
        Element name length exceeded <var>num</var> at line <var>line_num</var>(possibly
        around char <var>char_num</var>)",
        "detail": {
            "errorcode": "steps.xmlthreatprotection.ExecutionFailed"
        }
    }
}
</pre>

### Example Error response body

```
{
    "fault": {
        "faultstring": "XMLThreatProtection stepDefinition XML-Threat-Protection-1:
        Execution failed. reason: XMLThreatProtection stepDefinition XML-Threat-Protection-1:
        Element name length exceeded 5 at line 1(possibly around char 9)",
        "detail": {
            "errorcode": "steps.xmlthreatprotection.ExecutionFailed"
        }
    }
}
```

### Cause

This error occurs if the input payload specified by the policy's `<Source>` element contains XML elements that exceed the maximum length specified in the `<Element>` element underneath the `<NameLimits>` element.

For example, if the `<Element>` element is specified as `5` in XMLThreatProtection policy, but the input payload has an element which has more than 5 characters, then this error is thrown.

### Diagnosis

1.  Examine the error message to identify the XMLThreatProtection policy name, the line number, and the possible character number in the input payload where the element length was exceeded.

  For example, in the following error message, the policy name is `XML-Threat-Protection-1,`line number in the payload is `1`, and possible character number is `9.`

  ```
  XMLThreatProtection stepDefinition XML-Threat-Protection-1: Execution failed.
  reason: XMLThreatProtection stepDefinition XML-Threat-Protection-1: Element name
  length exceeded 5 at line 1(possibly around char 9)
  ```


1.  Examine the failed policy (identified in Step 1) and note the value specified in the `<Element>` element.

  For example, in the following XMLThreatProtection policy, `<Element>` is set to `5`:

  ```
  <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
  <XMLThreatProtection async="false" continueOnError="false" enabled="true" name="XML-Threat-Protection-1">
      <DisplayName>XML Threat Protection-1</DisplayName>
      <Properties/>
      <NameLimits>
          <Element>5</Element>
          <Attribute>5</Attribute>
          <NamespacePrefix>10</NamespacePrefix>
      <ProcessingInstructionTarget>10</ProcessingInstructionTarget>
      </NameLimits>
      <Source>request</Source>
      <StructureLimits>
          <NodeDepth>5</NodeDepth>
          <AttributeCountPerElement>2</AttributeCountPerElement>
          <NamespaceCountPerElement>3</NamespaceCountPerElement>
          <ChildCount includeComment="true" includeElement="true" includeProcessingInstruction="true" includeText="true">3</ChildCount>
      </StructureLimits>
      <ValueLimits>
          <Text>15</Text>
          <Attribute>10</Attribute>
          <NamespaceURI>10</NamespaceURI>
          <Comment>10</Comment>
          <ProcessingInstructionData>10</ProcessingInstructionData>
      </ValueLimits>
  </XMLThreatProtection>
  ```

1.  Examine the specific line number (identified in Step 1) of the input payload and check if the length of the element on that line is greater than the value specified for the `<Element>` element (identified in Step 2).  If the length of the element exceeds this value, then that is the cause of the error.

  Here is an example input payload:

  ```
  <company>
    <name>Google</name>
    <country>USA</country>
  </company>
  ```

  The XML payload shown above has a root element named `company` at line 1 which has 7 characters.  Since the length of the element is greater than 5 (the value specified for the `<Element>` element) you get the following error:


  ```
  XML-Threat-Protection-1: Execution failed. reason: XMLThreatProtection stepDefinition
  XML-Threat-Protection-1: Element name length exceeded 5 at line 1(possibly around char 9)
  ```

### Resolution

If the XMLThreadProtection policy is intended to protect against payloads with element names that exceed the defined value, then the error message is expected. In this case, there is no additional action required.

However, if you determine that longer elements can be specified in the payload without any consequence, then modify `<Element> `to a suitable value based on your requirements.

For example, If you think you can allow elements up to a length of 10, then modify the XMLThreatProtection policy as follows:

```
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<XMLThreatProtection async="false" continueOnError="false" enabled="true" name="XML-Threat-Protection-1">
    <DisplayName>XML Threat Protection-1</DisplayName>
    <Properties/>
    <NameLimits>
        <Element>10</Element>
        <Attribute>5</Attribute>
        <NamespacePrefix>10</NamespacePrefix>
        <ProcessingInstructionTarget>10</ProcessingInstructionTarget>
    </NameLimits>
    <Source>request</Source>
    <StructureLimits>
        <NodeDepth>5</NodeDepth>
        <AttributeCountPerElement>2</AttributeCountPerElement>
        <NamespaceCountPerElement>3</NamespaceCountPerElement>
        <ChildCount includeComment="true" includeElement="true" includeProcessingInstruction="true" includeText="true">3</ChildCount>
    </StructureLimits>
    <ValueLimits>
        <Text>15</Text>
        <Attribute>10</Attribute>
        <NamespaceURI>10</NamespaceURI>
        <Comment>10</Comment>
        <ProcessingInstructionData>10</ProcessingInstructionData>
    </ValueLimits>
</XMLThreatProtection>
```

## ChildCountExceeded


### Error Code

```
steps.xmlthreatprotection.ExecutionFailed
```

### Error response body

<pre class="prettyprint">
{
    "fault": {
        "faultstring": "XMLThreatProtection stepDefinition <var>policy_name</var>:
        Execution failed. reason: XMLThreatProtection stepDefinition <var>policy_name</var>:
        Children count exceeded <var>num</var> at line  <var>num</var>(possibly
        around char <var>num</var>)",
        "detail": {
            "errorcode": "steps.xmlthreatprotection.ExecutionFailed"
        }
    }
}
</pre>


### Example Error response body


```
{
    "fault": {
        "faultstring": "XMLThreatProtection stepDefinition XML-Threat-Protection-1:
        Execution failed. reason: XMLThreatProtection stepDefinition XML-Threat-Protection-1:
        Children count exceeded 3 at line 5(possibly around char 89)",
        "detail": {
            "errorcode": "steps.xmlthreatprotection.ExecutionFailed"
        }
    }
}
```

### Cause

This error occurs if the input message payload specified by the `<Source>` element contains an XML element that contains more child elements than the value specified in the `<ChildCount> `element of the policy.

For example, if the `<ChildCount> `element is 3, but the input XML payload has an element with more than 3 child elements, then this error is thrown.


### Diagnosis

1.  Examine the error message to identify the XMLThreatProtection policy name and the line number where the entry count is exceeded. For example, in the following error message, the policy name is `XML-Threat-Protection-1 `and line number in the payload is `5`:

  ```
  XMLThreatProtection stepDefinition XML-Threat-Protection-1: Execution failed.
  reason: XMLThreatProtection stepDefinition XML-Threat-Protection-1: Children
  count exceeded 3 at line 5(possibly around char 89)"
  ```

1.  Examine the failed policy (identified in Step 1) and note the value specified in the `<ChildCount>` element.

  In the following policy example, `<ChildCount>` is set  to `3 `(note that comments, include elements, and others count towards this limit):


  ```
  <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
  <XMLThreatProtection async="false" continueOnError="false" enabled="true" name="XML-Threat-Protection-1">
      <DisplayName>XML Threat Protection-1</DisplayName>
      <Properties/>
      <NameLimits>
          <Element>10</Element>
          <Attribute>5</Attribute>
          <NamespacePrefix>10</NamespacePrefix>
          <ProcessingInstructionTarget>10</ProcessingInstructionTarget>
      </NameLimits>
      <Source>request</Source>
      <StructureLimits>
          <NodeDepth>5</NodeDepth>
          <AttributeCountPerElement>2</AttributeCountPerElement>
          <NamespaceCountPerElement>3</NamespaceCountPerElement>
          <ChildCount includeComment="true" includeElement="true" includeProcessingInstruction="true" includeText="true">3</ChildCount>
      </StructureLimits>
      <ValueLimits>
          <Text>15</Text>
          <Attribute>10</Attribute>
          <NamespaceURI>10</NamespaceURI>
          <Comment>10</Comment>
          <ProcessingInstructionData>10</ProcessingInstructionData>
      </ValueLimits>
  </XMLThreatProtection>
  ```

1.  Examine the specific line number (identified in Step 1) of the input payload and check if the number of child elements (including all comments, etc) in the payload is greater than the value specified for the `<ChildCount>` element (identified in Step 2).  If the number of child elements exceeds the child count, then that is the cause of the error.

  Here is an example input payload:

  ```
  <movie>
    <title>Jurassic Park</title>
    <language>English</language>
    <country>USA</country>
    <director>Steven Spielberg</director>
  </movie>
  ```

  In the XML payload shown above, line 5 contains a fourth child element `<director> `of the root element `<movie>`.  Since the child element count in the input XML payload is greater than 3 (the value specified for the `<ChildCount>` element) you get the following error:


  ```
  XMLThreatProtection stepDefinition XML-Threat-Protection-1: Execution failed.
  reason: XMLThreatProtection stepDefinition XML-Threat-Protection-1: Children
  count exceeded 3 at line 5(possibly around char 89)
  ```

### Resolution

If the XMLThreatProtection policy is intended to protect against payloads with a child element count that exceeds a specific threshold, then the error message is expected. In this case, you don't need to take any additional action.

However, if you determine that a larger number of child elements (including comments, etc)  can be included in the payload without any consequence, then modify the `<ChildCount> `to a suitable value based on your requirements.

For example, If you think you can allow up to 10 child elements, then modify the XMLThreatProtection policy as follows:

```
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<XMLThreatProtection async="false" continueOnError="false" enabled="true" name="XML-Threat-Protection-1">
    <DisplayName>XML Threat Protection-1</DisplayName>
    <Properties/>
    <NameLimits>
        <Element>10</Element>
        <Attribute>5</Attribute>
        <NamespacePrefix>10</NamespacePrefix>
        <ProcessingInstructionTarget>10</ProcessingInstructionTarget>
    </NameLimits>
    <Source>request</Source>
    <StructureLimits>
        <NodeDepth>5</NodeDepth>
        <AttributeCountPerElement>2</AttributeCountPerElement>
        <NamespaceCountPerElement>3</NamespaceCountPerElement>
        <ChildCount includeComment="true" includeElement="true" includeProcessingInstruction="true" includeText="true">10</ChildCount>
    </StructureLimits>
    <ValueLimits>
        <Text>15</Text>
        <Attribute>10</Attribute>
        <NamespaceURI>10</NamespaceURI>
        <Comment>10</Comment>
        <ProcessingInstructionData>10</ProcessingInstructionData>
    </ValueLimits>
</XMLThreatProtection>
```


## NodeDepthExceeded


### Error Code

```
steps.xmlthreatprotection.ExecutionFailed
```

### Error response body


<pre class="prettyprint">
{
    "fault": {
        "faultstring": "XMLThreatProtection stepDefinition [<var>policy_name</var>]:
        Execution failed. reason: XMLThreatProtection stepDefinition [<var>policy_name</var>]:
        Node depth exceeded [<var>num</var>] at line [<var>num</var>](possibly around
        char [<var>num</var>])",
        "detail": {
            "errorcode": "steps.xmlthreatprotection.ExecutionFailed"
        }
    }
}
</pre>


### Example Error response body

```
{
    "fault": {
        "faultstring": "XMLThreatProtection stepDefinition XML-Threat-Protection-1:
        Execution failed. reason: XMLThreatProtection stepDefinition XML-Threat-Protection-1:
        Node depth exceeded 5 at line 6(possibly around char 110)",
        "detail": {
            "errorcode": "steps.xmlthreatprotection.ExecutionFailed"
        }
    }
}
```

### Cause

This error occurs if the input message payload specified by the `<Source>` element contains an XML document where the node hierarchy exceeds the number specified in the `<NodeDepth>` element of the XMLThreatProtection policy.

For example, if the `<NodeDepth>` element is specified as `4` in the policy, but the input payload has a node depth that exceeds `4`, then this error is thrown.

### Diagnosis

1.  Examine the error message to identify the XMLThreatProtection policy name and the line number where the node depth is exceeded. For example, in the following error message, the policy name is `XML-Threat-Protection-1 `and line number in the payload is `6`:

  ```
  XMLThreatProtection stepDefinition XML-Threat-Protection-1: Execution failed.
  reason: XMLThreatProtection stepDefinition XML-Threat-Protection-1: Node depth
  exceeded 5 at line 6(possibly around char 109)
  ```


1.  Examine the failed XMLThreatProtection policy (identified in Step 1) and note the value specified in the `<NodeDepth>` element.

  In the following XMLThreatProtection policy example, `<NodeDepth>` is set  to `5`:

  ```
  <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
  <XMLThreatProtection async="false" continueOnError="false" enabled="true" name="XML-Threat-Protection-1">
      <DisplayName>XML Threat Protection-1</DisplayName>
      <Properties/>
      <NameLimits>
          <Element>10</Element>
          <Attribute>5</Attribute>
          <NamespacePrefix>10</NamespacePrefix>
          <ProcessingInstructionTarget>10</ProcessingInstructionTarget>
      </NameLimits>
      <Source>request</Source>
      <StructureLimits>
          <NodeDepth>5</NodeDepth>
          <AttributeCountPerElement>2</AttributeCountPerElement>
          <NamespaceCountPerElement>3</NamespaceCountPerElement>
          <ChildCount includeComment="true" includeElement="true" includeProcessingInstruction="true" includeText="true">3</ChildCount>
      </StructureLimits>
      <ValueLimits>
          <Text>15</Text>
          <Attribute>10</Attribute>
          <NamespaceURI>10</NamespaceURI>
          <Comment>10</Comment>
          <ProcessingInstructionData>10</ProcessingInstructionData>
      </ValueLimits>
  </XMLThreatProtection>
  ```


1.  Examine the specific line number (identified in Step 1) of the input payload and check if the node depth specified there has a higher count than the number specified in the `<NodeDepth>` element (identified in Step 2).  If the level of XML child elements exceeds the count, then that is the cause of the error.

  Here is an example input payload:

  ```
  <hellos>
     <hello1>
         <wave1>
              <wave2>
                  <wave3>
                      <wave4>
                         <wave5>wave</wave5>
                      </wave4>
                  </wave3>
              </wave2>
         </wave1>
     </hello1>
     <hello2>world2</hello2>
     <hello3>world3</hello3>
     <hello4>world4</hello4>
     <hello5>world5</hello5>
     <hello6>world6</hello6>
  </hellos>
  ```

  The XML Payload shown above  at line 6 has a node depth of 6`. `Because the node depth is greater than 5 (the value specified for the `<NodeDepth>` element) you get the following error:

  ```
  XMLThreatProtection stepDefinition XML-Threat-Protection-1: Execution failed.
  reason: XMLThreatProtection stepDefinition XML-Threat-Protection-1: Node depth
  exceeded 5 at line 6(possibly around char 109)
  ```

### Resolution

If the XMLThreatProtection policy is intended to protect against payloads with the specific node depth threshold, then the error message is expected. In this case, there is no additional action required.

However, if you determine that  a greater node depth can be allowed, then modify `<NodeDepth> `to a suitable value based on your requirements.

For example, If you think you can allow a node depth of up to 10, then modify the policy as follows:

```
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<XMLThreatProtection async="false" continueOnError="false" enabled="true" name="XML-Threat-Protection-1">
    <DisplayName>XML Threat Protection-1</DisplayName>
    <Properties/>
    <NameLimits>
        <Element>10</Element>
        <Attribute>5</Attribute>
        <NamespacePrefix>10</NamespacePrefix>
        <ProcessingInstructionTarget>10</ProcessingInstructionTarget>
    </NameLimits>
    <Source>request</Source>
    <StructureLimits>
        <NodeDepth>10</NodeDepth>
        <AttributeCountPerElement>2</AttributeCountPerElement>
        <NamespaceCountPerElement>3</NamespaceCountPerElement>
        <ChildCount includeComment="true" includeElement="true" includeProcessingInstruction="true" includeText="true">3</ChildCount>
    </StructureLimits>
    <ValueLimits>
        <Text>15</Text>
        <Attribute>10</Attribute>
        <NamespaceURI>10</NamespaceURI>
        <Comment>10</Comment>
        <ProcessingInstructionData>10</ProcessingInstructionData>
    </ValueLimits>
</XMLThreatProtection>
```


## AttributeCountExceeded

### Error Code

```
steps.xmlthreatprotection.ExecutionFailed
```

### Error response body


<pre class="prettyprint>
{
    "fault": {
        "faultstring": "XMLThreatProtection stepDefinition [<var>policy_name</var>]:
        Execution failed. reason: XMLThreatProtection stepDefinition [<var>policy_name</var>]:
        Attribute count exceeded [<var>num</var>] at line [<var>num</var>](possibly around
        char [<var>num</var>])",
        "detail": {
            "errorcode": "steps.xmlthreatprotection.ExecutionFailed"
        }
    }
}
</pre>

### Example Error response body

```
{
    "fault": {
        "faultstring": "XMLThreatProtection stepDefinition XML-Threat-Protection-1:
        Execution failed. reason: XMLThreatProtection stepDefinition XML-Threat-Protection-1:
        Attribute count exceeded 2 at line 3(possibly around char 105)",
        "detail": {
            "errorcode": "steps.xmlthreatprotection.ExecutionFailed"
        }
    }
}
```

### Cause

This error occurs if the input message payload specified by the XMLThreatProtection policy's `<Source>` element contains an XML document where the number of attributes of a single element exceeds the number specified in the `<AttributeCountPerElement>` element of the policy.

For example, if the `<AttributeCountPerElement>` element is specified as `2` in the` `XMLThreatProtection policy, but the input payload has an element with more than 2 attributes, then this error is thrown.


### Diagnosis


1.  Examine the error message to identify the XMLThreatProtection policy name and the line number where the number of attributes is exceeded. For example, in the following error message, the policy name is `XMLThreat-Protection-1 `and line number in the payload is `3`:

  ```
  XMLThreatProtection stepDefinition XML-Threat-Protection-1: Execution failed.
  reason: XMLThreatProtection stepDefinition XML-Threat-Protection-1: Attribute
  count exceeded 2 at line 3(possibly around char 105)
  ```

1.  Examine the failed policy (identified in Step 1), and note the value specified in the `<AttributeCountPerElement>` element.

  In the following policy example, `<AttributeCountPerElement>` is set  to `2`:


  ```
  <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
  <XMLThreatProtection async="false" continueOnError="false" enabled="true" name="XML-Threat-Protection-1">
      <DisplayName>XML Threat Protection-1</DisplayName>
      <Properties/>
      <NameLimits>
          <Element>10</Element>
          <Attribute>5</Attribute>
          <NamespacePrefix>10</NamespacePrefix>
          <ProcessingInstructionTarget>10</ProcessingInstructionTarget>
      </NameLimits>
      <Source>request</Source>
      <StructureLimits>
          <NodeDepth>10</NodeDepth>
          <AttributeCountPerElement>2</AttributeCountPerElement>
          <NamespaceCountPerElement>3</NamespaceCountPerElement>
          <ChildCount includeComment="true" includeElement="true" includeProcessingInstruction="true" includeText="true">3</ChildCount>
      </StructureLimits>
      <ValueLimits>
          <Text>15</Text>
          <Attribute>10</Attribute>
          <NamespaceURI>10</NamespaceURI>
          <Comment>10</Comment>
          <ProcessingInstructionData>10</ProcessingInstructionData>
      </ValueLimits>
  </XMLThreatProtection>
  ```

1.  Examine the specific line number (identified in Step 1) of the input payload and check if the number of attributes specified exceeds the number specified in the `<AttributeCountPerElementh>` element (identified in Step 2).  If the number of attributes exceeds the count, then that is the cause of the error.

  Here is an example input payload:

  ```
  <?xml version="1.0" encoding="UTF-8"?>
  <bookstore>
    <book category="cooking" cuisine="Indian" lang="en">
      <title>Easy Indian Cooking</title>
      <author>Suneeta</author>
      <year>2004</year>
      <price>300.00</price>
    </book>
  </bookstore>
  ```

  The XML payload shown above  at line 3 has an element with three attributes. Because the attribute count is greater than 2 (the value specified for the `<AttributeCountPerElement>` element) you get the following error:


  ```
  XMLThreatProtection stepDefinition XML-Threat-Protection-1: Execution failed.
  reason: XMLThreatProtection stepDefinition XML-Threat-Protection-1: Attribute
  count exceeded 2 at line 3(possibly around char 105)
  ```

### Resolution

If the XMLThreatProtection policy is intended to protect against payloads that exceed a specific number of attributes, then the error message is expected. In this case, there is no additional action required.

However, if you determine that a greater number of attributes can be allowed, then modify `<AttributeCountPerElement> `to a suitable value based on your requirements.

For example, if you think you can allow a number of attributes per element of up to 5, then modify the policy as follows:


```
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<XMLThreatProtection async="false" continueOnError="false" enabled="true" name="XML-Threat-Protection-1">
    <DisplayName>XML Threat Protection-1</DisplayName>
    <Properties/>
    <NameLimits>
        <Element>10</Element>
        <Attribute>5</Attribute>
        <NamespacePrefix>10</NamespacePrefix>
        <ProcessingInstructionTarget>10</ProcessingInstructionTarget>
    </NameLimits>
    <Source>request</Source>
    <StructureLimits>
        <NodeDepth>10</NodeDepth>
        <AttributeCountPerElement>5</AttributeCountPerElement>
        <NamespaceCountPerElement>3</NamespaceCountPerElement>
        <ChildCount includeComment="true" includeElement="true" includeProcessingInstruction="true" includeText="true">3</ChildCount>
    </StructureLimits>
    <ValueLimits>
        <Text>15</Text>
        <Attribute>10</Attribute>
        <NamespaceURI>10</NamespaceURI>
        <Comment>10</Comment>
        <ProcessingInstructionData>10</ProcessingInstructionData>
    </ValueLimits>
</XMLThreatProtection>
```

## AttributeNameExceeded


### Error Code

```
steps.xmlthreatprotection.ExecutionFailed
```

### Error response body


<pre class="prettyprint">
{
    "fault": {
        "faultstring": "XMLThreatProtection stepDefinition [<var>policy_name</var>]:
        Execution failed. reason: XMLThreatProtection stepDefinition [<var>policy_name</var>]:
        Attribute name length exceeded [<var>num</var>] at line [<var>num</var>](possibly around char [<var>num</var>])",
        "detail": {
            "errorcode": "steps.xmlthreatprotection.ExecutionFailed"
        }
    }
}
</pre>



### Example Error response body

```
{
    "fault": {
        "faultstring": "XMLThreatProtection stepDefinition XML-Threat-Protection-1:
        Execution failed. reason: XMLThreatProtection stepDefinition XML-Threat-Protection-1:
        Attribute name length exceeded 5 at line 3(possibly around char 105)",
        "detail": {
            "errorcode": "steps.xmlthreatprotection.ExecutionFailed"
        }
    }
}
```

### Cause

This error occurs if the input message payload specified by the XMLThreatProtection policy's `<Source>` element contains an XML document where an attribute name exceeds the number of characters specified in the `<Attribute>` child element of the `<NameLimits>` element of the policy.

For example, if the `<Attribute>` element is specified as `5` in the policy, but the input payload has an attribute name with more than 5 characters, then this error is thrown.


### Diagnosis

1.  Examine the error message to identify the XMLThreatProtection policy name and the line number where the attribute name length is exceeded. For example, in the following error message, the policy name is `XML-Threat-Protection-1 `and line number in the payload is `3`:

  ```
  XMLThreatProtection stepDefinition XML-Threat-Protection-1: Execution failed. 
  reason: XMLThreatProtection stepDefinition XML-Threat-Protection-1: Attribute 
  name length exceeded 5 at line 3(possibly around char 105)
  ```

1.  Examine the failed policy (identified in Step 1) and note the value specified in the `<Attribute>` element

  In the following XMLThreatProtection policy example, `<Attribute>` is set to `5`:

  ```
  <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
  <XMLThreatProtection async="false" continueOnError="false" enabled="true" name="XML-Threat-Protection-1">
      <DisplayName>XML Threat Protection-1</DisplayName>
      <Properties/>
      <NameLimits>
          <Element>10</Element>
          <Attribute>5</Attribute>
          <NamespacePrefix>10</NamespacePrefix>
          <ProcessingInstructionTarget>10</ProcessingInstructionTarget>
      </NameLimits>
      <Source>request</Source>
      <StructureLimits>
          <NodeDepth>10</NodeDepth>
          <AttributeCountPerElement>2</AttributeCountPerElement>
          <NamespaceCountPerElement>3</NamespaceCountPerElement>
          <ChildCount includeComment="true" includeElement="true" includeProcessingInstruction="true" includeText="true">3</ChildCount>
      </StructureLimits>
      <ValueLimits>
          <Text>15</Text>
          <Attribute>10</Attribute>
          <NamespaceURI>10</NamespaceURI>
          <Comment>10</Comment>
          <ProcessingInstructionData>10</ProcessingInstructionData>
      </ValueLimits>
  </XMLThreatProtection>
  ```

1.  Examine the specific line number (identified in Step 1) of the input payload and check if the length of the attribute specified there has more characters than the number specified in the `<Attribute>` element (identified in Step 2).  If the number of characters exceeds the attribute value, then that is the cause of the error.

  Here is an example input payload:

  ```
  <?xml version="1.0" encoding="UTF-8"?>
  <bookstore>
    <book category="cooking" cuisine="Indian" lang="en">
      <title>Easy Indian Cooking</title>
      <author>Suneeta</author>
      <year>2004</year>
      <price>300.00</price>
    </book>
  </bookstore>
  ```

  The XML Payload shown above  at line 3 has two attributes `category` and `cuisine` with name length of 8 and 7 characters respectively`.` Because the attribute name length is greater than 5 (the value specified for `<Attribute>` element) you get the following error:

  ```
  XMLThreatProtection stepDefinition XML-Threat-Protection-1: Execution failed.
  reason: XMLThreatProtection stepDefinition XML-Threat-Protection-1: Attribute
  name length exceeded 5 at line 3(possibly around char 105)
  ```

### Resolution

If the XMLThreatProtection policy is intended to protect against payloads which exceed a specific attribute name length, then the error message is expected. In this case, there is no additional action required.

However, if you determine that a longer attribute name length can be allowed, then modify `<Attribute>` to a suitable value based on your requirements.

For example, If you think you can have attribute names of up to 10 characters, then modify the policy as follows:


```
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<XMLThreatProtection async="false" continueOnError="false" enabled="true" name="XML-Threat-Protection-1">
    <DisplayName>XML Threat Protection-1</DisplayName>
    <Properties/>
    <NameLimits>
        <Element>10</Element>
        <Attribute>10</Attribute>
        <NamespacePrefix>10</NamespacePrefix>
        <ProcessingInstructionTarget>10</ProcessingInstructionTarget>
    </NameLimits>
    <Source>request</Source>
    <StructureLimits>
        <NodeDepth>10</NodeDepth>
        <AttributeCountPerElement>5</AttributeCountPerElement>
        <NamespaceCountPerElement>3</NamespaceCountPerElement>
        <ChildCount includeComment="true" includeElement="true" includeProcessingInstruction="true" includeText="true">3</ChildCount>
    </StructureLimits>
    <ValueLimits>
        <Text>15</Text>
        <Attribute>10</Attribute>
        <NamespaceURI>10</NamespaceURI>
        <Comment>10</Comment>
        <ProcessingInstructionData>10</ProcessingInstructionData>
    </ValueLimits>
</XMLThreatProtection>
```

## AttributeValueExceeded


### Error Code

```
steps.xmlthreatprotection.ExecutionFailed
```

### Error response body


<pre class="prettyprint">
{
    "fault": {
        "faultstring": "XMLThreatProtection stepDefinition [<var>policy_name</var>]: Execution failed. reason: XMLThreatProtection stepDefinition [<var>policy_name</var>]:  Attribute value length exceeded [<var>num</var>] at line [<var>num</var>](possibly around char [<var>num</var>])",
        "detail": {
            "errorcode": "steps.xmlthreatprotection.ExecutionFailed"
        }
    }
}
</pre>


### Example Error response body

```
{
    "fault": {
        "faultstring": "XMLThreatProtection stepDefinition XML-Threat-Protection-1:
        Execution failed. reason: XMLThreatProtection stepDefinition XML-Threat-Protection-1:
        Attribute value length exceeded 10 at line 3(possibly around char 111)",
        "detail": {
            "errorcode": "steps.xmlthreatprotection.ExecutionFailed"
        }
    }
}
```

### Cause

This error occurs if the input message payload specified by the policy's `<Source>` element contains an XML document where an attribute value exceeds the number of characters specified in the `<Attribute>` child element of the `<ValueLimits>` element.

For example, if the `<Attribute>` element is specified as `10` in the XMLThreatProtection policy, but the input payload has an attribute value with more than 10 characters, then this error is thrown.


### Diagnosis

1.  Examine the error message to identify the XMLThreatProtection policy name and the line number where the number of attributes is exceeded. For example, in the following error message, the policy name is `XML-Threat-Protection-1 `and line number in the payload is `3`:

  ```
  XMLThreatProtection stepDefinition XML-Threat-Protection-1: Execution failed. 
  reason: XMLThreatProtection stepDefinition XML-Threat-Protection-1: Attribute
  value length exceeded 10 at line 3(possibly around char 111)
  ```

1.  Examine the failed policy (identified in Step 1) and note the value specified in the `<Attribute>` child element of the `<ValueLimits>` element.

  In the following XMLThreatProtection policy example, `<Attribute>` is set  to 10:

  ```
  <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
  <XMLThreatProtection async="false" continueOnError="false" enabled="true" name="XML-Threat-Protection-1">
      <DisplayName>XML Threat Protection-1</DisplayName>
      <Properties/>
      <NameLimits>
          <Element>10</Element>
          <Attribute>5</Attribute>
          <NamespacePrefix>10</NamespacePrefix>
          <ProcessingInstructionTarget>10</ProcessingInstructionTarget>
      </NameLimits>
      <Source>request</Source>
      <StructureLimits>
          <NodeDepth>10</NodeDepth>
          <AttributeCountPerElement>2</AttributeCountPerElement>
          <NamespaceCountPerElement>3</NamespaceCountPerElement>
          <ChildCount includeComment="true" includeElement="true" includeProcessingInstruction="true" includeText="true">3</ChildCount>
      </StructureLimits>
      <ValueLimits>
          <Text>15</Text>
          <Attribute>10</Attribute>
          <NamespaceURI>10</NamespaceURI>
          <Comment>10</Comment>
          <ProcessingInstructionData>10</ProcessingInstructionData>
      </ValueLimits>
  </XMLThreatProtection>
  ```


1.  Examine the specific line number (identified in Step 1) of the input payload and check if the length of the attribute value specified there has more characters than the number specified in the `<Attribute>` element (identified in Step 2).  If the number of characters exceeds the attribute value, then that is the cause of the error.

  Here is an example input payload:

  ```
  <?xml version="1.0" encoding="UTF-8"?>
  <bookstore>
    <book category="cooking" cuisine="South Indian" lang="en">
      <title>Easy Indian Cooking</title>
      <author>Suneeta</author>
      <year>2004</year>
      <price>300.00</price>
    </book>
  </bookstore>
  ```

  The XML Payload shown above  at line 3 has an attribute (cuisine) with a value length of 12 characters. Because the attribute value length is greater than 10 (the value specified for the `<Attribute>` element) you get the following error:

  ```
  XMLThreatProtection stepDefinition XML-Threat-Protection-1: Execution failed.
  reason: XMLThreatProtection stepDefinition XML-Threat-Protection-1: Attribute
  value length exceeded 10 at line 3(possibly around char 111)
  ```

### Resolution

If the XMLThreatProtection policy is intended to protect against payloads that exceed a specific attribute value length, then the error message is expected. In this case, there is no additional action required.

However, if you determine that longer attribute values can be allowed, then modify the `<Attribute> `child element of the `<ValueLimits>` element to a suitable value based on your requirements.

For example, If you think you can attribute values of up to 15 characters, then modify the policy as follows:

```
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<XMLThreatProtection async="false" continueOnError="false" enabled="true" name="XML-Threat-Protection-1">
    <DisplayName>XML Threat Protection-1</DisplayName>
    <Properties/>
    <NameLimits>
        <Element>10</Element>
        <Attribute>10</Attribute>
        <NamespacePrefix>10</NamespacePrefix>
        <ProcessingInstructionTarget>10</ProcessingInstructionTarget>
    </NameLimits>
    <Source>request</Source>
    <StructureLimits>
        <NodeDepth>10</NodeDepth>
        <AttributeCountPerElement>5</AttributeCountPerElement>
        <NamespaceCountPerElement>3</NamespaceCountPerElement>
        <ChildCount includeComment="true" includeElement="true" includeProcessingInstruction="true" includeText="true">3</ChildCount>
    </StructureLimits>
    <ValueLimits>
        <Text>15</Text>
        <Attribute>15</Attribute>
        <NamespaceURI>10</NamespaceURI>
        <Comment>10</Comment>
        <ProcessingInstructionData>10</ProcessingInstructionData>
    </ValueLimits>
</XMLThreatProtection>
```

## TextExceeded

### Error Code

```
steps.xmlthreatprotection.ExecutionFailed
```

### Error response body

<pre class="prettyprint">
{
    "fault": {
        "faultstring": "XMLThreatProtection stepDefinition [<var>policy_name</var>]: Execution failed. reason: XMLThreatProtection stepDefinition [<var>policy_name</var>]:  Text length exceeded [<var>num</var>] at line [<var>num</var>](possibly around char [<var>num</var>])",
        "detail": {
            "errorcode": "steps.xmlthreatprotection.ExecutionFailed"
        }
    }
}
</pre>

### Example Error response body

```
{
    "fault": {
        "faultstring": "XMLThreatProtection stepDefinition XML-Threat-Protection-1:
        Execution failed. reason: XMLThreatProtection stepDefinition XML-Threat-Protection-1:
        Text length exceeded 15 at line 4(possibly around char 66)",
        "detail": {
            "errorcode": "steps.xmlthreatprotection.ExecutionFailed"
        }
    }
```

### Cause

This error occurs if the input message payload specified by the XMLThreatProtection policy's `<Source>` element contains an XML document where the text length of an element exceeds the number of characters specified in the `<Text>` child element of the `<ValueLimits>` element of the policy.

For example, if the `<Text>` element is specified as `15` in the policy, but the input payload has a text with more than 15 characters, then this error is thrown.

### Diagnosis

1.  Examine the error message to identify the XMLThreatProtection policy name and the line number where the number of attributes is exceeded. For example, in the following error message, the policy name is `XML-Threat-Protection-1` and the line number in the payload is `4`:

  ```
  XMLThreatProtection stepDefinition XML-Threat-Protection-1: Execution failed.
  reason: XMLThreatProtection stepDefinition XML-Threat-Protection-1: Text length
  exceeded 15 at line 4(possibly around char 66)
  ```

1.  Examine the failed policy (identified in Step 1) and note the value specified in the  `<Text>` child element of the `<ValueLimits>` element.

  In the following XMLThreatProtection policy example, `<Text>` is set  to `15`:

  ```
  <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
  <XMLThreatProtection async="false" continueOnError="false" enabled="true" name="XML-Threat-Protection-1">
      <DisplayName>XML Threat Protection-1</DisplayName>
      <Properties/>
      <NameLimits>
          <Element>10</Element>
          <Attribute>5</Attribute>
          <NamespacePrefix>10</NamespacePrefix>
          <ProcessingInstructionTarget>10</ProcessingInstructionTarget>
      </NameLimits>
      <Source>request</Source>
      <StructureLimits>
          <NodeDepth>10</NodeDepth>
          <AttributeCountPerElement>2</AttributeCountPerElement>
          <NamespaceCountPerElement>3</NamespaceCountPerElement>
          <ChildCount includeComment="true" includeElement="true" includeProcessingInstruction="true" includeText="true">3</ChildCount>
      </StructureLimits>
      <ValueLimits>
          <Text>15</Text>
          <Attribute>10</Attribute>
          <NamespaceURI>10</NamespaceURI>
          <Comment>10</Comment>
          <ProcessingInstructionData>10</ProcessingInstructionData>
      </ValueLimits>
  </XMLThreatProtection>
  ```


1.  Examine the specific line number (identified in Step 1) of the input payload and check if the length of the attribute value specified there has more characters than the number specified in the `<Text>` element (identified in Step 2).  If the number of characters exceeds the attribute value, then that is the cause of the error.

  ```
  <food>
    <name>Veg Sandwich</name>
    <price>$5.95</price>
    <description>Vegetarian Sandwich with onions, tomatoes and lettuce</description>
    <calories>650</calories>
  </food>
  ```

  The XML Payload shown above  at line 4 has text with a length of 53 characters. Because the attribute value length is greater than 15 (the value specified for the `<Text>` element) you get the following error:

  ```
  XMLThreatProtection stepDefinition XML-Threat-Protection-1: Execution failed.
  reason: XMLThreatProtection stepDefinition XML-Threat-Protection-1: Text length
  exceeded 15 at line 4(possibly around char 66)
  ```

### Resolution

If the XMLThreatProtection policy is intended to protect against payloads which exceed a specific text length, then the error message is expected. In this case, there is no additional action required.

However, if you determine that longer text lengths can be allowed, then modify the `<Text> `child element of the `<ValueLimits>` element to a suitable value based on your requirements.

For example, if you think you can have attribute values of up to 60 characters, then modify the policy as follows:

```
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<XMLThreatProtection async="false" continueOnError="false" enabled="true" name="XML-Threat-Protection-1">
    <DisplayName>XML Threat Protection-1</DisplayName>
    <Properties/>
    <NameLimits>
        <Element>10</Element>
        <Attribute>10</Attribute>
        <NamespacePrefix>10</NamespacePrefix>
        <ProcessingInstructionTarget>10</ProcessingInstructionTarget>
    </NameLimits>
    <Source>request</Source>
    <StructureLimits>
        <NodeDepth>10</NodeDepth>
        <AttributeCountPerElement>5</AttributeCountPerElement>
        <NamespaceCountPerElement>3</NamespaceCountPerElement>
        <ChildCount includeComment="true" includeElement="true" includeProcessingInstruction="true" includeText="true">3</ChildCount>
    </StructureLimits>
    <ValueLimits>
        <Text>60</Text>
        <Attribute>15</Attribute>
        <NamespaceURI>10</NamespaceURI>
        <Comment>10</Comment>
        <ProcessingInstructionData>10</ProcessingInstructionData>
    </ValueLimits>
</XMLThreatProtection>
```

## CommentExceeded


### Error Code

```
steps.xmlthreatprotection.ExecutionFailed
```

### Error response body

<pre class="prettyprint">
{
    "fault": {
        "faultstring": "XMLThreatProtection stepDefinition [<var>policy_name</var>]:
        Execution failed. reason: XMLThreatProtection stepDefinition [<var>policy_name</var>]: 
        Comment length exceeded [<var>num</var>] at line [<var>num</var>](possibly around char
        [<var>num</var>])",
        "detail": {
            "errorcode": "steps.xmlthreatprotection.ExecutionFailed"
        }
    }
}
</pre>


### Example Error response body

```
{
    "fault": {
        "faultstring": "XMLThreatProtection stepDefinition XML-Threat-Protection-1:
        Execution failed. reason: XMLThreatProtection stepDefinition XML-Threat-Protection-1:
        Comment length exceeded 10 at line 2(possibly around char 48)",
        "detail": {
            "errorcode": "steps.xmlthreatprotection.ExecutionFailed"
        }
    }
}
```


### Cause

This error occurs if the input message payload specified by the XMLThreatProtection policy's `<Source>` element contains an XML document where a comment length exceeds the number of characters specified in the `<Comment>` child element of the `<ValueLimits>` element of the policy.

For example, if the `<Comment>` element is specified as `10` in the policy, but the input payload has a comment with more than 10 characters, then this error is thrown.


### Diagnosis



1.  Examine the error message to identify the XMLThreatProtection policy name and the line number where the comment length is exceeded. For example, in the following error message, the policy name is `XML-Threat-Protection-1 `and the line number in the payload is `2`:

  ```
  XMLThreatProtection stepDefinition XML-Threat-Protection-1: Execution failed. reason: XMLThreatProtection stepDefinition XML-Threat-Protection-1: Comment length exceeded 10 at line 2(possibly around char 48)
  ```

1.  Examine the failed policy (identified in Step 1) and note the value specified in the `<Comment>` child element of the `<ValueLimits>` element.

  In the following XMLThreatProtection policy example, `<Comment>` is set  to `10`:

  ```
  <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
  <XMLThreatProtection async="false" continueOnError="false" enabled="true" name="XML-Threat-Protection-1">
      <DisplayName>XML Threat Protection-1</DisplayName>
      <Properties/>
      <NameLimits>
          <Element>10</Element>
          <Attribute>5</Attribute>
          <NamespacePrefix>10</NamespacePrefix>
          <ProcessingInstructionTarget>10</ProcessingInstructionTarget>
      </NameLimits>
      <Source>request</Source>
      <StructureLimits>
          <NodeDepth>10</NodeDepth>
          <AttributeCountPerElement>2</AttributeCountPerElement>
          <NamespaceCountPerElement>3</NamespaceCountPerElement>
          <ChildCount includeComment="true" includeElement="true" includeProcessingInstruction="true" includeText="true">3</ChildCount>
      </StructureLimits>
      <ValueLimits>
          <Text>15</Text>
          <Attribute>10</Attribute>
          <NamespaceURI>10</NamespaceURI>
          <Comment>10</Comment>
          <ProcessingInstructionData>10</ProcessingInstructionData>
      </ValueLimits>
  </XMLThreatProtection>
  ```

1.  Examine the specific line number (identified in Step 1) of the input payload and check if the length of the comment specified there has more characters than the number specified in the `<Comment>` element (identified in Step 2).  If the number of characters exceeds the attribute value, then that is the cause of the error.

  Here is an example input payload:

  ```
  <food>
    <!--This is somewhat a long comment-->
    <name>Veg Sandwich</name>
    <price>$5.95</price>
    <description>Vegetarian Sandwich with onions, tomatoes and lettuce</description>
    <calories>650</calories>
  </food>
  ```

  The XML Payload shown above  at line 2 has a comment with a value length of 31 characters`.` Because the comment length is greater than 10 (the value specified for the `<Comment>` element) you get the following error:

  ```
  XMLThreatProtection stepDefinition XML-Threat-Protection-1: Execution failed.
  reason: XMLThreatProtection stepDefinition XML-Threat-Protection-1: Comment length
  exceeded 10 at line 2(possibly around char 48)
  ```

### Resolution

If the XMLThreatProtection policy is intended to protect against payloads that exceed a specific comment length, then the error message is expected. In this case, there is no additional action required.

However, if you determine that a longer comment can be allowed, then modify the `<Comment> `child element of the` <ValueLimits>` element to a suitable value based on your requirements.

For example, If you think you can attribute values of up to 40 characters, then modify the policy as follows:

```
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<XMLThreatProtection async="false" continueOnError="false" enabled="true" name="XML-Threat-Protection-1">
    <DisplayName>XML Threat Protection-1</DisplayName>
    <Properties/>
    <NameLimits>
        <Element>10</Element>
        <Attribute>10</Attribute>
        <NamespacePrefix>10</NamespacePrefix>
        <ProcessingInstructionTarget>10</ProcessingInstructionTarget>
    </NameLimits>
    <Source>request</Source>
    <StructureLimits>
        <NodeDepth>10</NodeDepth>
        <AttributeCountPerElement>5</AttributeCountPerElement>
        <NamespaceCountPerElement>3</NamespaceCountPerElement>
        <ChildCount includeComment="true" includeElement="true" includeProcessingInstruction="true" includeText="true">3</ChildCount>
    </StructureLimits>
    <ValueLimits>
        <Text>15</Text>
        <Attribute>15</Attribute>
        <NamespaceURI>10</NamespaceURI>
        <Comment>40</Comment>
        <ProcessingInstructionData>10</ProcessingInstructionData>
    </ValueLimits>
</XMLThreatProtection>
```


## PIDataExceeded

### Error Code

```
steps.xmlthreatprotection.ExecutionFailed
```

### Error response body

<pre class="prettyprint">
{
    "fault": {
        "faultstring": "XMLThreatProtection stepDefinition [<var>policy_name</var>]:
        Execution failed. reason: XMLThreatProtection stepDefinition [<var>policy_name</var>]:
        Processing Instruction data length exceeded [<var>num</var>] at line
        [<var>num</var>](possibly around char [<var>num</var>])",
        "detail": {
            "errorcode": "steps.xmlthreatprotection.ExecutionFailed"
        }
    }
}
</pre>


### Example Error response body

```
{
    "fault": {
        "faultstring": "XMLThreatProtection stepDefinition XML-Threat-Protection-1:
        Execution failed. reason: XMLThreatProtection stepDefinition XML-Threat-Protection-1:
        Processing Instruction data length exceeded 30 at line 2(possibly around char 109)",
        "detail": {
            "errorcode": "steps.xmlthreatprotection.ExecutionFailed"
        }
    }
}
```

### Cause

This error occurs if the input message payload specified by the XMLThreatProtection policy's `<Source>` element contains an XML document where a processing data instruction exceeds the number of characters specified in the `<ProcessingInstructionData>` child element of the `<ValueLimits> `element of the policy.

For example, if the `<ProcessingInstructionData>` element is specified as `10` in the XMLThreatProtection policy, but the input payload has a processing instruction with more than 10 characters, then this error is thrown.

Note: Refer to the [Extensible Markup Language specification](https://www.w3.org/TR/xml/#sec-pi) to learn about processing instructions.


### Diagnosis

1.  Examine the error message to identify the XMLThreatProtection policy name and the line number where the processing instruction length is exceeded. For example, in the following error message, the policy name is `XML-Threat-Protection-1 `and line number in the payload is `2`:

  ```
  XMLThreatProtection stepDefinition XML-Threat-Protection-1: Execution failed.
  reason: XMLThreatProtection stepDefinition XML-Threat-Protection-1: Processing
  Instruction data length exceeded 30 at line 2(possibly around char 109)
  ```

1.  Examine the failed policy (identified in Step 1) and note the value specified in the `<ProcessingInstructionData>` child element of the `<ValueLimits>` element .

  In the following XMLThreatProtection policy example, `<ProcessingInstructionData>` is set  to `30`:

  ```
  <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
  <XMLThreatProtection async="false" continueOnError="false" enabled="true" name="XML-Threat-Protection-1">
      <DisplayName>XML Threat Protection-1</DisplayName>
      <Properties/>
      <NameLimits>
          <Element>10</Element>
          <Attribute>5</Attribute>
          <NamespacePrefix>10</NamespacePrefix>
          <ProcessingInstructionTarget>10</ProcessingInstructionTarget>
      </NameLimits>
      <Source>request</Source>
      <StructureLimits>
          <NodeDepth>10</NodeDepth>
          <AttributeCountPerElement>2</AttributeCountPerElement>
          <NamespaceCountPerElement>3</NamespaceCountPerElement>
          <ChildCount includeComment="true" includeElement="true" includeProcessingInstruction="true" includeText="true">3</ChildCount>
      </StructureLimits>
      <ValueLimits>
          <Text>15</Text>
          <Attribute>10</Attribute>
          <NamespaceURI>10</NamespaceURI>
          <Comment>10</Comment>
          <ProcessingInstructionData>30</ProcessingInstructionData>
      </ValueLimits>
  </XMLThreatProtection>
  ```

1.  Examine the specific line number (identified in Step 1) of the input payload and check if the length of the processing instruction data specified there has more characters than the number specified in the `<ProcessingInstructionData>` element (identified in Step 2).  If the number of characters exceeds the processing instruction data, then that is the cause of the error.

  Here is an example input payload:

  ```
  <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
  <?xml-stylesheet type="text/xsl" href="mobilephones.xsl"?>
  <mobilephones>
    <mobilephone>
        <name>iPhone</name>
        <price>$600</price>
    </mobilephone>
    <mobilephone>
        <name>Samsung</name>
        <price>$500</price>
    </mobilephone>
  </mobilephones>
```

  The XML payload shown above  at line 1 has a processing instruction data element (`type="text/xsl" href="mobilephones.xsl"?) `with a length of 40 characters`.` Because this length is greater than 30 (the value specified for the `<ProcessingInstructionData>` element) you get the following error:

  ```
  XMLThreatProtection stepDefinition XML-Threat-Protection-1: Execution failed.
  reason: XMLThreatProtection stepDefinition XML-Threat-Protection-1: Processing
  Instruction data length exceeded 30 at line 2(possibly around char 109)
  ```

### Resolution

If the XMLThreatProtection policy is intended to protect against payloads that exceed a specific processing instruction data length, then the error message is expected. In this case, there is no additional action required.

However, if you determine that longer processing instruction data elements can be allowed, then modify the `<ProcessingInstructionData>` child element of the `<ValueLimits>` element to a suitable value based on your requirements.

For example, If you think you can have data processing instruction lengths of up to 50 characters, then modify the policy as follows:

```
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<XMLThreatProtection async="false" continueOnError="false" enabled="true" name="XML-Threat-Protection-1">
    <DisplayName>XML Threat Protection-1</DisplayName>
    <Properties/>
    <NameLimits>
        <Element>10</Element>
        <Attribute>10</Attribute>
        <NamespacePrefix>10</NamespacePrefix>
        <ProcessingInstructionTarget>10</ProcessingInstructionTarget>
    </NameLimits>
    <Source>request</Source>
    <StructureLimits>
        <NodeDepth>10</NodeDepth>
        <AttributeCountPerElement>5</AttributeCountPerElement>
        <NamespaceCountPerElement>3</NamespaceCountPerElement>
        <ChildCount includeComment="true" includeElement="true" includeProcessingInstruction="true" includeText="true">3</ChildCount>
    </StructureLimits>
    <ValueLimits>
        <Text>15</Text>
        <Attribute>15</Attribute>
        <NamespaceURI>10</NamespaceURI>
        <Comment>40</Comment>
        <ProcessingInstructionData>50</ProcessingInstructionData>
    </ValueLimits>
</XMLThreatProtection>
```


## PITargetExceeded

### Error Code

```
steps.xmlthreatprotection.ExecutionFailed
```

### Error response body

<pre class="prettyprint">
{
    "fault": {
        "faultstring": "XMLThreatProtection stepDefinition [<var>policy_name</var>]:
        Execution failed. reason: XMLThreatProtection stepDefinition [<var>policy_name</var>]: 
        Processing Instruction target length exceeded [<var>num</var>] at line [<var>num</var>](possibly around
        char [<var>num</var>])",
        "detail": {
            "errorcode": "steps.xmlthreatprotection.ExecutionFailed"
        }
    }
}
</pre>

### Example Error response body

```
{
    "fault": {
        "faultstring": "XMLThreatProtection stepDefinition XML-Threat-Protection-1:
        Execution failed. reason: XMLThreatProtection stepDefinition XML-Threat-Protection-1:
        Processing Instruction target length exceeded 10 at line 2(possibly around char 114)",
        "detail": {
            "errorcode": "steps.xmlthreatprotection.ExecutionFailed"
        }
    }
}
```

### Cause

This error occurs if the input message payload specified by the XMLThreatProtection policy's `<Source>` element contains an XML document where a process instruction target exceeds the number of characters specified in the `<ProcessingInstructionTarget>` child element of the `<NameLimits> `element of the policy.

For example, if the `<ProcessingInstructionTarget>` element is specified as `10` in the XMLThreatProtection policy, but the input payload has a process instruction target with more than 10 characters, then this error is thrown.

Note: Refer to the [Extensible Markup Language specification](https://www.w3.org/TR/xml/#sec-pi) to learn about processing instructions.

### Diagnosis

1.  Examine the error message to identify the XMLThreatProtection policy name and the line number where the number of processing instruction target is exceeded. For example, in the following error message, the policy name is `XML-Threat-Protection-1 `and line number in the payload is `1`:

    ```
    XMLThreatProtection stepDefinition XML-Threat-Protection-1: Execution failed.
    reason: XMLThreatProtection stepDefinition XML-Threat-Protection-1: Processing
    Instruction target length exceeded 10 at line 2(possibly around char 114)
    ```

1.  Examine the failed XMLThreatProtection policy (identified in Step #1) and note the value specified in the `<ProcessingInstructionTarget>` child element of the `<NameLimits`> element.

  In the following XMLThreatProtection policy example, `<ProcessingInstructionTarget>` is set  to `10`:

  ```
  <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
  <XMLThreatProtection async="false" continueOnError="false" enabled="true" name="XML-Threat-Protection-1">
      <DisplayName>XML Threat Protection-1</DisplayName>
      <Properties/>
      <NameLimits>
          <Element>10</Element>
          <Attribute>10</Attribute>
          <NamespacePrefix>10</NamespacePrefix>
      <ProcessingInstructionTarget>10</ProcessingInstructionTarget>
      </NameLimits>
      <Source>request</Source>
      <StructureLimits>
          <NodeDepth>10</NodeDepth>
          <AttributeCountPerElement>5</AttributeCountPerElement>
          <NamespaceCountPerElement>3</NamespaceCountPerElement>
          <ChildCount includeComment="true" includeElement="true" includeProcessingInstruction="true" includeText="true">3</ChildCount>
      </StructureLimits>
      <ValueLimits>
          <Text>20</Text>
          <Attribute>15</Attribute>
          <NamespaceURI>40</NamespaceURI>
          <Comment>10</Comment>
          <ProcessingInstructionData>30</ProcessingInstructionData>
      </ValueLimits>
  </XMLThreatProtection>
  ```

1.  Examine the specific line number (identified in Step 1) of the input payload and check if the length of the processing instruction target specified there has more characters than the number specified in the `<ProcessingInstructionTarget>` element (identified in Step 2).  If the number of characters exceeds the target size limit, then that is the cause of the error.

  Here is an example input payload:

  ```
  <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
  <?xml-stylesheet type="text/xsl" href="mobilephones.xsl"?>
  <mobilephones>
    <mobilephone>
        <name>iPhone</name>
        <price>$600</price>
    </mobilephone>
    <mobilephone>
        <name>Samsung</name>
        <price>$500</price>
    </mobilephone>
  </mobilephones>
  ```

  The XML Payload shown above  at line 2 has a processing instruction target name "`xml-stylesheet" `with a length of 14 characters. Because the processing instruction target name is longer than 10 (the value specified for `<ProcessingInstructionTarget>` element) you get the following error:

  ```
  XMLThreatProtection stepDefinition XML-Threat-Protection-1: Execution failed.
  reason: XMLThreatProtection stepDefinition XML-Threat-Protection-1: Processing
  Instruction target length exceeded 10 at line 2(possibly around char 114)
  ```

### Resolution

If the XMLThreatProtection policy is intended to protect against payloads that exceed a specific process instruction target length, then the error message is expected. In this case, there is no additional action required.

However, if you determine that a process instruction target can be longer, then modify the `<ProcessingInstructionTarget> `child element of the `<NameLimits>` element to a suitable value based on your requirements.

For example, If you think you can have process instruction targets of up to 25 characters, then modify the policy as follows:


  ```
  <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
  <XMLThreatProtection async="false" continueOnError="false" enabled="true" name="XML-Threat-Protection-1">
    <DisplayName>XML Threat Protection-1</DisplayName>
    <Properties/>
    <NameLimits>
        <Element>10</Element>
        <Attribute>5</Attribute>
        <NamespacePrefix>15</NamespacePrefix>
    <ProcessingInstructionTarget>25</ProcessingInstructionTarget>
    </NameLimits>
    <Source>request</Source>
    <StructureLimits>
        <NodeDepth>10</NodeDepth>
        <AttributeCountPerElement>2</AttributeCountPerElement>
        <NamespaceCountPerElement>3</NamespaceCountPerElement>
        <ChildCount includeComment="true" includeElement="true" includeProcessingInstruction="true" includeText="true">3</ChildCount>
    </StructureLimits>
    <ValueLimits>
        <Text>15</Text>
        <Attribute>10</Attribute>
        <NamespaceURI>40</NamespaceURI>
        <Comment>10</Comment>
        <ProcessingInstructionData>10</ProcessingInstructionData>
    </ValueLimits>
  </XMLThreatProtection>
  ```


## NSURIExceeded

### Error Code

```
steps.xmlthreatprotection.ExecutionFailed
```

### Error response body

<pre class="prettyprint">
{
    "fault": {
        "faultstring": "XMLThreatProtection stepDefinition [<var>policy_name</var>]: Execution failed. reason: XMLThreatProtection stepDefinition [<var>policy_name</var>]:  Namespace uri length length exceeded [<var>num</var>] at line [<var>num</var>](possibly around char [<var>num</var>])",
        "detail": {
            "errorcode": "steps.xmlthreatprotection.ExecutionFailed"
        }
    }
}
</pre>


### Example Error response body

```
{
    "fault": {
        "faultstring": "XMLThreatProtection stepDefinition XML-Threat-Protection-1:
        Execution failed. reason: XMLThreatProtection stepDefinition XML-Threat-Protection-1:
        Namespace uri length exceeded 10 at line 2(possibly around char 97)",
        "detail": {
            "errorcode": "steps.xmlthreatprotection.ExecutionFailed"
        }
    }
}
```

### Cause

This error occurs if the input message payload specified by the XMLThreatProtection policy's `<Source>` element contains an XML document where a [Namespace](https://www.w3.org/TR/xml-names/) URI exceeds the number of characters specified in the `<NamespaceURI>` child element of the `<ValueLimits> `element of the policy.

For example, if the `<NamespaceURI>` element is specified as `10` in the policy, but the input payload has a namespace URI with more than 10 characters, then this error is thrown.

### Diagnosis

1.  Examine the error message to identify the XMLThreatProtection policy name and the line number where the namespace URI length is exceeded. For example, in the following error message, the policy name is `XML-Threat-Protection-1 `and line number in the payload is `2`:

  ```
  XMLThreatProtection stepDefinition XML-Threat-Protection-1: Execution failed.
  reason: XMLThreatProtection stepDefinition XML-Threat-Protection-1: Namespace
  uri length exceeded 10 at line 2(possibly around char 97)
  ```


1.  Examine the failed policy (identified in Step 1) and note the value specified in the `<NamespaceURI>` child element of the `<ValueLimits`> element.

  In the following XMLThreatProtection policy example, `<NamespaceURI>` is set  to `10`:

  ```
  <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
  <XMLThreatProtection async="false" continueOnError="false" enabled="true" name="XML-Threat-Protection-1">
      <DisplayName>XML Threat Protection-1</DisplayName>
      <Properties/>
      <NameLimits>
          <Element>10</Element>
          <Attribute>5</Attribute>
          <NamespacePrefix>10</NamespacePrefix>
          <ProcessingInstructionTarget>10</ProcessingInstructionTarget>
      </NameLimits>
      <Source>request</Source>
      <StructureLimits>
          <NodeDepth>10</NodeDepth>
          <AttributeCountPerElement>2</AttributeCountPerElement>
          <NamespaceCountPerElement>3</NamespaceCountPerElement>
          <ChildCount includeComment="true" includeElement="true" includeProcessingInstruction="true" includeText="true">3</ChildCount>
      </StructureLimits>
      <ValueLimits>
          <Text>15</Text>
          <Attribute>10</Attribute>
          <NamespaceURI>10</NamespaceURI>
          <Comment>10</Comment>
          <ProcessingInstructionData>10</ProcessingInstructionData>
      </ValueLimits>
  </XMLThreatProtection>
  ```

1.  Examine the specific line number (identified in Step 1) of the input payload and check if the length of the namespace URI specified there has more characters than the number specified in the `<NamespaceURI>` element (identified in Step 2).  If the number of characters exceeds the namespace URI  length, then that is the cause of the error.

  Here is an example input payload:

  ```
  <?xml version = "1.0" encoding = "UTF-8"?>
    <profile:user xmlns:profile = "www.google.com/profile">
    <profile:name>Rama Krishna</profile:name>
    <profile:company>Google</profile:company>
    <profile:phone>(91) 9876543210</profile:phone>
  </profile:user>
  ```

  The XML Payload shown above  at line 2 has a namespace URI, [www.google.com/profile](http://www.googlecom/profile), with a length of 22 characters. Because the namespace URI length is greater than 10 (the value specified for the `<NamespaceURI>` element) you get the following error:

  ```
  XMLThreatProtection stepDefinition XML-Threat-Protection-1: Execution failed.
  reason: XMLThreatProtection stepDefinition XML-Threat-Protection-1: Namespace
  uri length exceeded 10 at line 2(possibly around char 99)
  ```

### Resolution

If the XMLThreatProtection policy is intended to protect against payloads which exceeded a specific namespace URI length, then the error message is expected. In this case, there is no additional action required.

However, if you determine that a longer namespace URI can be allowed, then modify the `<NamespaceURI>` child element of the `<ValueLimits>` element to a suitable value based on your requirements.

For example, If you think you can have namespace URI lengths of up to 30 characters, then modify the policy as follows:

```
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<XMLThreatProtection async="false" continueOnError="false" enabled="true" name="XML-Threat-Protection-1">
    <DisplayName>XML Threat Protection-1</DisplayName>
    <Properties/>
    <NameLimits>
        <Element>10</Element>
        <Attribute>5</Attribute>
        <NamespacePrefix>10</NamespacePrefix>
        <ProcessingInstructionTarget>10</ProcessingInstructionTarget>
    </NameLimits>
    <Source>request</Source>
    <StructureLimits>
        <NodeDepth>10</NodeDepth>
        <AttributeCountPerElement>2</AttributeCountPerElement>
        <NamespaceCountPerElement>3</NamespaceCountPerElement>
        <ChildCount includeComment="true" includeElement="true" includeProcessingInstruction="true" includeText="true">3</ChildCount>
    </StructureLimits>
    <ValueLimits>
        <Text>15</Text>
        <Attribute>10</Attribute>
        <NamespaceURI>30</NamespaceURI>
        <Comment>10</Comment>
        <ProcessingInstructionData>10</ProcessingInstructionData>
    </ValueLimits>
</XMLThreatProtection>
```


## NSPrefixExceeded

### Error Code

```
steps.xmlthreatprotection.ExecutionFailed
```

### Error response body

<pre class="prettyprint">
{
    "fault": {
        "faultstring": "XMLThreatProtection stepDefinition [<var>policy_name</var>]:
        Execution failed. reason: XMLThreatProtection stepDefinition [<var>policy_name</var>]: 
        Namespace prefix length exceeded [<var>num</var>] at line [<var>num</var>](possibly
        around char [<var>num</var>])",
        "detail": {
            "errorcode": "steps.xmlthreatprotection.ExecutionFailed"
        }
    }
}
</pre>


### Example Error response body

```
{
    "fault": {
        "faultstring": "XMLThreatProtection stepDefinition XML-Threat-Protection-1:
        Execution failed. reason: XMLThreatProtection stepDefinition XML-Threat-Protection-1:
        Namespace prefix length exceeded 10 at line 2(possibly around char 105)",
        "detail": {
            "errorcode": "steps.xmlthreatprotection.ExecutionFailed"
        }
    }
}
```

### Cause

This error occurs if the input message payload specified by the `<Source>` element contains an XML document where a [Namespace](https://www.w3.org/TR/xml-names/) prefix exceeds the number of characters specified in the `<NamespacePrefix>` child element of the `<NameLimits>` element of the policy.

For example, if the `<NamespacePrefix>` element is specified as `10` in the XMLThreatProtection policy, but the input payload has a namespace prefix with more than 10 characters, then this error is thrown.


### Diagnosis


1.  Examine the error message to identify the XMLThreatProtection policy name and the line number where the namespace URI prefix length is exceeded. For example, in the following error message, the policy name is `XML-Threat-Protection-1` and line number in the payload is `2`:

  ```
  XMLThreatProtection stepDefinition XML-Threat-Protection-1: Execution failed.
  reason: XMLThreatProtection stepDefinition XML-Threat-Protection-1: Namespace
  prefix length exceeded 10 at line 2(possibly around char 105)
  ```

1.  Examine the failed policy (identified in Step 1) and note the value specified in the `<NamespacePrefix>` child element of the `<NameLimits`> element.

  In the following XMLThreatProtection policy example, `<NamespacePrefix>` is set  to `10`:

  ```
  <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
  <XMLThreatProtection async="false" continueOnError="false" enabled="true" name="XML-Threat-Protection-1">
      <DisplayName>XML Threat Protection-1</DisplayName>
      <Properties/>
      <NameLimits>
          <Element>10</Element>
          <Attribute>10</Attribute>
          <NamespacePrefix>10</NamespacePrefix>
          <ProcessingInstructionTarget>10</ProcessingInstructionTarget>
      </NameLimits>
      <Source>request</Source>
      <StructureLimits>
          <NodeDepth>10</NodeDepth>
          <AttributeCountPerElement>5</AttributeCountPerElement>
          <NamespaceCountPerElement>3</NamespaceCountPerElement>
          <ChildCount includeComment="true" includeElement="true" includeProcessingInstruction="true" includeText="true">3</ChildCount>
      </StructureLimits>
      <ValueLimits>
          <Text>20</Text>
          <Attribute>15</Attribute>
          <NamespaceURI>40</NamespaceURI>
          <Comment>10</Comment>
          <ProcessingInstructionData>30</ProcessingInstructionData>
      </ValueLimits>
  </XMLThreatProtection>
  ```

1.  Examine the specific line number (identified in Step 1) of the input payload and check if the length of the namespace prefix specified there has more characters than the number specified in the `<NamespacePrefix>` element (identified in Step 2).  If the number of characters exceeds the namespace prefix limit, then that is the cause of the error.

  Here is an example input payload:

  ```
  <?xml version = "1.0" encoding = "UTF-8"?>
  <userprofile:user xmlns:userprofile = "www.googlecom/profile">
    <userprofile:name>Rama Krishna</userprofile:name>
    <userprofile:company>Google</userprofile:company>
    <userprofile:phone>(011) 123-4567</userprofile:phone>
  </userprofile:user>
  ```

  The XML Payload shown above  at line 2 has a namespace prefix `userprofile` with a length of 11 characters. Because the namespace URI prefix length is greater than 10 (the value specified for `<NamespacePrefix>` element) you get the following error:

  ```
  XMLThreatProtection stepDefinition XML-Threat-Protection-1: Execution failed.
  reason: XMLThreatProtection stepDefinition XML-Threat-Protection-1: Namespace
  prefix length exceeded 10 at line 2(possibly around char 105)
  ```

### Resolution

If the XMLThreatProtection policy is intended to protect against payloads that exceed a specific namespace URI prefix length, then the error message is expected. In this case, there is no additional action required.

However, if you determine that a longer namespace URI prefix can be allowed, then modify the `<NamespacePrefix>` child element of the `<NameLimits>` element to a suitable value based on your requirements.

For example, if you think you can have namespace URI prefix lengths of up to 15 characters, then modify the policy as follows:

```
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<XMLThreatProtection async="false" continueOnError="false" enabled="true" name="XML-Threat-Protection-1">
    <DisplayName>XML Threat Protection-1</DisplayName>
    <Properties/>
    <NameLimits>
        <Element>10</Element>
        <Attribute>5</Attribute>
        <NamespacePrefix>15</NamespacePrefix>
        <ProcessingInstructionTarget>10</ProcessingInstructionTarget>
    </NameLimits>
    <Source>request</Source>
    <StructureLimits>
        <NodeDepth>10</NodeDepth>
        <AttributeCountPerElement>2</AttributeCountPerElement>
        <NamespaceCountPerElement>3</NamespaceCountPerElement>
        <ChildCount includeComment="true" includeElement="true" includeProcessingInstruction="true" includeText="true">3</ChildCount>
    </StructureLimits>
    <ValueLimits>
        <Text>15</Text>
        <Attribute>10</Attribute>
        <NamespaceURI>40</NamespaceURI>
        <Comment>10</Comment>
        <ProcessingInstructionData>10</ProcessingInstructionData>
    </ValueLimits>
</XMLThreatProtection>
```


## NSCountExceeded

### Error Code

```
steps.xmlthreatprotection.ExecutionFailed
```

### Error response body

<pre class="prettyprint">
{
    "fault": {
        "faultstring": "XMLThreatProtection stepDefinition [<var>policy_name</var>]:
        Execution failed. reason: XMLThreatProtection stepDefinition [<var>policy_name</var>]:
        Namespace count exceeded [<var>num</var>] at line [<var>num</var>](possibly around char [<var>num</var>])",
        "detail": {
            "errorcode": "steps.xmlthreatprotection.ExecutionFailed"
        }
    }
}
</pre>

### Example Error response body

```
{
    "fault": {
        "faultstring": "XMLThreatProtection stepDefinition XML-Threat-Protection-1: Execution failed. reason: XMLThreatProtection stepDefinition XML-Threat-Protection-1: Namespace count exceeded 3 at line 2(possibly around char 234)",
        "detail": {
            "errorcode": "steps.xmlthreatprotection.ExecutionFailed"
        }
    }
}
```

### Cause

This error occurs if the input message payload specified by the XMLThreatProtection policy's `<Source>` element contains an XML document where the number of namespaces used in a single element exceeds the number specified in the `<NamespaceCountPerElement>` child element of the `<StructureLimits> `element of the policy.

For example, if the `<NamespaceCountPerElement>` element is specified as `3` in the policy, but the input payload has an element with more than 3 namespaces, then this error is thrown.

### Diagnosis

1.  Examine the error message to identify the XMLThreatProtection policy name and the line number where the number of namespaces is exceeded. For example, in the following error message, the policy name is `XMLThreat-Protection-1 `and line number in the payload is `2`:

  ```
  XMLThreatProtection stepDefinition XML-Threat-Protection-1: Execution failed.
  reason: XMLThreatProtection stepDefinition XML-Threat-Protection-1: Namespace count
  exceeded 3 at line 2(possibly around char 234)
  ```

1.  Examine the failed policy (identified in Step 1) and note the value specified in the `<NamespaceCountPerElement>` child element of the `<StructureLimits`> element.

  In the following XMLThreatProtection policy example, `<NamespaceCountPerElement>` is set  to `3`:

  ```
  <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
  <XMLThreatProtection async="false" continueOnError="false" enabled="true" name="XML-Threat-Protection-1">
      <DisplayName>XML Threat Protection-1</DisplayName>
      <Properties/>
      <NameLimits>
          <Element>10</Element>
          <Attribute>10</Attribute>
          <NamespacePrefix>10</NamespacePrefix>
          <ProcessingInstructionTarget>10</ProcessingInstructionTarget>
      </NameLimits>
      <Source>request</Source>
      <StructureLimits>
          <NodeDepth>10</NodeDepth>
          <AttributeCountPerElement>5</AttributeCountPerElement>
          <NamespaceCountPerElement>3</NamespaceCountPerElement>
          <ChildCount includeComment="true" includeElement="true" includeProcessingInstruction="true" includeText="true">3</ChildCount>
      </StructureLimits>
      <ValueLimits>
          <Text>20</Text>
          <Attribute>15</Attribute>
          <NamespaceURI>40</NamespaceURI>
          <Comment>10</Comment>
          <ProcessingInstructionData>30</ProcessingInstructionData>
      </ValueLimits>
  </XMLThreatProtection>
  ```

1.  Examine the specific line number (identified in Step 1) of the input payload and check if the number of namespaces specified there for a single element is higher than the number specified in the `<NamespaceCountPerElement>` element (identified in Step 2).  If the number of namespaces exceeds the namespace count per element limit, then that is the cause of the error.

  Here is an example input payload:

  ```
  <?xml version = "1.0" encoding = "UTF-8"?>
  <hellos xmlns:h="https://www.w3schools.com/greeting1" xmlns:a="https://www.w3schools.com/greeting2" xmlns:b="https://www.w3schols.com/greeting3" xmlns:c="https://www.w3schools.com/greeting4">
      <a:hello1>world1</a:hello1>
      <b:hello2>world2</b:hello2>
      <c:hello2>world2</c:hello2>
      <h:hello2>world2</h:hello2>
  </hellos>
  ```

  The XML Payload shown above  at line 2 has more than 3 namespaces. Because the namespace count is greater than 3 (the value specified for `<NamespaceCountPerElement>` element) you get the following error:

  ```
  XMLThreatProtection stepDefinition XML-Threat-Protection-1: Execution failed.
  reason: XMLThreatProtection stepDefinition XML-Threat-Protection-1: Namespace
  count exceeded 3 at line 2(possibly around char 234)
  ```

### Resolution

If the XMLThreatProtection policy is intended to protect against payloads that exceed a specific number of namespaces in a single element, then the error message is expected. In this case, there is no additional action required.

However, if you determine that a larger number of namespaces can be allowed, then modify `<NamespaceCountPerElement>` child element of the `<StructureLimits>` element to a suitable value based on your requirements.

For example, If you think you can have up to 5 namespaces in a single element, then modify the policy as follows:

```
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<XMLThreatProtection async="false" continueOnError="false" enabled="true" name="XML-Threat-Protection-1">
    <DisplayName>XML Threat Protection-1</DisplayName>
    <Properties/>
    <NameLimits>
        <Element>10</Element>
        <Attribute>10</Attribute>
        <NamespacePrefix>10</NamespacePrefix>
        <ProcessingInstructionTarget>10</ProcessingInstructionTarget>
    </NameLimits>
    <Source>request</Source>
    <StructureLimits>
        <NodeDepth>10</NodeDepth>
        <AttributeCountPerElement>5</AttributeCountPerElement>
        <NamespaceCountPerElement>5</NamespaceCountPerElement>
        <ChildCount includeComment="true" includeElement="true" includeProcessingInstruction="true" includeText="true">3</ChildCount>
    </StructureLimits>
    <ValueLimits>
        <Text>20</Text>
        <Attribute>15</Attribute>
        <NamespaceURI>40</NamespaceURI>
        <Comment>10</Comment>
        <ProcessingInstructionData>30</ProcessingInstructionData>
    </ValueLimits>
</XMLThreatProtection>
```

## InvalidXMLPayload

### Error Code

```
steps.xmlthreatprotection.ExecutionFailed
```

### Error response body

<pre class="prettyprint">
{
    "fault": {
        "faultstring": "XMLThreatProtection stepDefinition [<var>policy_name</var>]:
        Execution failed. Reason: [<var>error_description</var>]",
        "detail": {
            "errorcode": "steps.xmlthreatprotection.ExecutionFailed"
        }
    }
}
</pre>


### Example Error response body

```
{
    "fault": {
        "faultstring": "XMLThreatProtection stepDefinition XML-Threat-Protection-1:
        Execution failed. reason: Syntax error while parsing XML prologue",
        "detail": {
            "errorcode": "steps.xmlthreatprotection.ExecutionFailed"
        }
    }
}
```

### Cause

This error occurs if the input message payload specified by the XMLProtectionPolicy policy's `<Source>` element is not a valid XML Document.

Note: The error description followed by "`reason:`" can vary depending on what is incorrect in the XML payload.


### Diagnosis

1.  Examine the error message to identify the policy name where the error occurred. In the following example the policy name is `XML-Threat-Protection-1`.

  ```
  XMLThreatProtection stepDefinition XML-Threat-Protection-1: Execution failed.
  reason: Syntax error while parsing XML prologue
  ```

1.  Examine the input payload and check if the XML document that was passed in the payload is indeed a well formed XML document or not.

  Here is an example input payload:

  ```
  <?xml version="1.0" encoding="UTF-8"?>
  <bookstore>
    <book category="cooking">
      <title lang="en">Easy Indian Cooking</title>
      <author>Suneeta</author>
      <year>2004</year>
      <price>300.00</price>
    </book>
  <bookstore>
  ```
  In the XML payload shown above, the line 9 does not close the root element correctly as `/` is missing. Because this is not well formed XML, you get the error:

  ```
  XMLThreatProtection stepDefinition XML-Threat-Protection-1: Execution failed.
  reason: Syntax error while parsing XML prologue
  ```

### Resolution

Ensure a valid input XML payload is passed to any API proxy that includes the XMLThreatProtection policy.

To correct the example error described above, modify the input XML payload as follows:

```
<?xml version="1.0" encoding="UTF-8"?>
<bookstore>
  <book category="cooking">
    <title lang="en">Easy Indian Cooking
    <author>Suneeta</author>
    <year>2004</year>
    <price>300.00</price>
  </book>
</bookstore>
```

## SourceUnavailable

### Error Code

```
steps.xmlthreatprotection.SourceUnavailable
```

### Error response body

<pre class="prettyprint">
{
    "fault": {
        "faultstring": "XMLThreatProtection stepDefinition[<var>policy_name</var>]: Source[<var>var_name</var>] is not available"
        "detail": {
            "errorcode": "steps.xmlthreatprotection.SourceUnavailable"
        }
    }
}
</pre>


### Example Error response body

```
{
    "fault": {
        "faultstring": "XMLThreatProtection stepDefinition XML-Threat-Protection-1:
        Source requests is not available",
        "detail": {
            "errorcode": "steps.xmlthreatprotection.SourceUnavailable"
        }
    }
}
```

### Cause

This error occurs if the [message](https://docs.apigee.com/api-platform/reference/variables-reference#messagevariables) variable specified in the `<Source>` element of the XMLThreatProtection policy is either:

*   Out of scope (not available in the specific flow where the policy is being executed)
*   Is not one of the valid values <code><em>request</em></code>, <code><em>response</em></code>, or <code><em>message</em></code>

For example, this error occurs if the `<Source>` element in the policy is set to a variable that doesn't exist in the flow where the policy is executed.

### Diagnosis

1.  Identify the XMLThreatProtection policy name, and the name of the Source variable from the error message. For example, in the following error message, the XMLThreatProtection policy name is `XML-Threat-Protection-1 `and the Source variable is `requests:` 
 
  ```
  XMLThreatProtection stepDefinition XML-Threat-Protection-1: Source requests
  is not available 
  ```
1.  Examine the failed XMLThreatProtection policy and check the value specified for the `<Source>` element identified in Step 1. 

  In the following XMLThreatProtection policy example, the `<Source>` element is set  to `requests`.  

  ```
  <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
  <XMLThreatProtection async="false" continueOnError="false" enabled="true" name="XML-Threat-Protection-1">
      <DisplayName>XML Threat Protection-1</DisplayName>
      <Properties/>
      <NameLimits>
          <Element>10</Element>
          <Attribute>10</Attribute>
          <NamespacePrefix>10</NamespacePrefix>
          <ProcessingInstructionTarget>10</ProcessingInstructionTarget>
      </NameLimits>
      <Source>requests</Source>
      <StructureLimits>
          <NodeDepth>10</NodeDepth>
          <AttributeCountPerElement>5</AttributeCountPerElement>
          <NamespaceCountPerElement>3</NamespaceCountPerElement>
          <ChildCount includeComment="true" includeElement="true" includeProcessingInstruction="true" includeText="true">3</ChildCount>
      </StructureLimits>
      <ValueLimits>
          <Text>20</Text>
          <Attribute>15</Attribute>
          <NamespaceURI>40</NamespaceURI>
          <Comment>10</Comment>
          <ProcessingInstructionData>30</ProcessingInstructionData>
      </ValueLimits>
  </XMLThreatProtection>
  ```

  The valid values for `<Source>` element are <code><em>request</em></code>, <code><em>response</em></code>, or <code><em>message.</em></code> Because <code>requests</code> is not a valid value and doesn't exist in the flow where the policy is being executed, you get the error:

  ```
  XMLThreatProtection stepDefinition XML-Threat-Protection-1: Source requests
  is not available
  ```

### Resolution

Ensure that the variable set in the `<Source>` element of the failed XMLThreatProtection policy is set to either `request`, `response`, or `message` and exists in the flow where the policy executes.

To correct the example policy shown above, you could modify the `<Source>` element to use the <code>request<em> </em></code>variable, because it exists in the request flow:

```
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<XMLThreatProtection async="false" continueOnError="false" enabled="true" name="XML-Threat-Protection-1">
    <DisplayName>XML Threat Protection-1</DisplayName>
    <Properties/>
    <NameLimits>
        <Element>10</Element>
        <Attribute>10</Attribute>
        <NamespacePrefix>10</NamespacePrefix>
        <ProcessingInstructionTarget>10</ProcessingInstructionTarget>
    </NameLimits>
    <Source>request</Source>
    <StructureLimits>
        <NodeDepth>10</NodeDepth>
        <AttributeCountPerElement>5</AttributeCountPerElement>
        <NamespaceCountPerElement>3</NamespaceCountPerElement>
        <ChildCount includeComment="true" includeElement="true" includeProcessingInstruction="true" includeText="true">3</ChildCount>
    </StructureLimits>
    <ValueLimits>
        <Text>20</Text>
        <Attribute>15</Attribute>
        <NamespaceURI>40</NamespaceURI>
        <Comment>10</Comment>
        <ProcessingInstructionData>30</ProcessingInstructionData>
    </ValueLimits>
</XMLThreatProtection>
```

## NonMessageVariable


### Error Code

```
steps.xmlthreatprotection.NonMessageVariable
```

### Error response body

<pre class="prettyprint">
{
    "fault": {
        "faultstring": "Variable <var>var_name</var> does not resolve to a Message"
        "detail": {
            "errorcode": "steps.xmlthreatprotection.NonMessageVariable"
        }
    }
}
</pre>

### Example Error response body

```
{
    "fault": {
        "faultstring": "Variable message.content does not resolve to a Message",
        "detail": {
            "errorcode": "steps.xmlthreatprotection.NonMessageVariable"
        }
    }
}
```

### Cause

This error occurs if the `<Source>` element in the XMLThreatProtection policy is set to a variable which is not of type [Message](https://docs.apigee.com/api-platform/reference/variables-reference#messagevariables).

Message type variables represent entire HTTP requests and responses. The built-in Apigee Edge flow variables `request`, `response`, and `message` are of type Message. To learn more about message variables, see the [Variables reference](https://docs.apigee.com/api-platform/reference/variables-reference#messagevariables).

### Diagnosis

1.  Identify the name of the variable that does resolve to a Message type of variable from the error message. For example, in the following error message the name of variable is `message.content`

  ```
  Variable message.content does not resolve to a Message
  ```

1.  Examine all the XMLThreadProtection policies in the API proxy where the failure occurred and identify the specific XMLThreadProtection policy where  `<Source>` element is specified with the variable name identified in Step 1 above.


  In the following XMLThreatProtection policy example, `<Source>` is set  to `message.content`:


  ```
  <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
  <XMLThreatProtection async="false" continueOnError="false" enabled="true" name="XML-Threat-Protection-1">
      <DisplayName>XML Threat Protection-1</DisplayName>
      <Properties/>
      <NameLimits>
          <Element>10</Element>
          <Attribute>10</Attribute>
          <NamespacePrefix>10</NamespacePrefix>
          <ProcessingInstructionTarget>10</ProcessingInstructionTarget>
      </NameLimits>
      <Source>message.content</Source>
      <StructureLimits>
          <NodeDepth>10</NodeDepth>
          <AttributeCountPerElement>5</AttributeCountPerElement>
          <NamespaceCountPerElement>3</NamespaceCountPerElement>
          <ChildCount includeComment="true" includeElement="true" includeProcessingInstruction="true" includeText="true">3</ChildCount>
      </StructureLimits>
      <ValueLimits>
          <Text>20</Text>
          <Attribute>15</Attribute>
          <NamespaceURI>40</NamespaceURI>
          <Comment>10</Comment>
          <ProcessingInstructionData>30</ProcessingInstructionData>
      </ValueLimits>
  </XMLThreatProtection>
  ```

  Because the variable  `message.content` is not of type Message, you get the error:

  ```
  Variable message.content does not resolve to a Message
  ```



### Resolution

Ensure that the `<Source>` element in the failed XMLThreatProtection policy is set to a [Message](https://docs.apigee.com/api-platform/reference/variables-reference#messagevariables) type flow variable that exists in the flow where the policy executes.

To correct the policy, you can modify the `<Source>` element to specify a variable that is of type Message. For example, in the failed XMLThreatProtection you could specify the `<Source>` element as `message`:


```
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<XMLThreatProtection async="false" continueOnError="false" enabled="true" name="XML-Threat-Protection-1">
    <DisplayName>XML Threat Protection-1</DisplayName>
    <Properties/>
    <NameLimits>
        <Element>10</Element>
        <Attribute>10</Attribute>
        <NamespacePrefix>10</NamespacePrefix>
        <ProcessingInstructionTarget>10</ProcessingInstructionTarget>
    </NameLimits>
    <Source>message</Source>
    <StructureLimits>
        <NodeDepth>10</NodeDepth>
        <AttributeCountPerElement>5</AttributeCountPerElement>
        <NamespaceCountPerElement>3</NamespaceCountPerElement>
        <ChildCount includeComment="true" includeElement="true" includeProcessingInstruction="true" includeText="true">3</ChildCount>
    </StructureLimits>
    <ValueLimits>
        <Text>20</Text>
        <Attribute>15</Attribute>
        <NamespaceURI>40</NamespaceURI>
        <Comment>10</Comment>
        <ProcessingInstructionData>30</ProcessingInstructionData>
    </ValueLimits>
</XMLThreatProtection>
```

{% endblock %}
