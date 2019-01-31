{% extends "_base.html" %}
{% block title %}ExtensionCallout policy{% endblock %}
{% block body %}

{{ extensions_availability }}

Use the ExtensionCallout policy to incorporate an extension into an API proxy.

An extension provides access to a specific resource external to Apigee Edge. The resource could be Google Cloud Platform services such as [Cloud Storage](https://cloud.google.com/storage/docs/) or [Cloud Speech-to-Text](https://cloud.google.com/speech-to-text/docs/). But the resource could be any external resource accessible over HTTP or HTTPS.

For an overview of extensions, see [What are extensions?](/api-platform/extensions/extensions-overview) For an introductory tutorial, see [Tutorial: Adding and using an extension](/api-platform/tutorials/add-extension/intro).

Before accessing an extension from the ExtensionCallout policy, you must [add, configure, and deploy](/api-platform/extensions/configuring-an-extension) the extension from an extension package that is already installed into your Apigee Edge organization.

### Samples

#### Stackdriver Logging example

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<ConnectorCallout async="false" continueOnError="true" enabled="true" name="Logging-Extension">
		<DisplayName>Logging Extension</DisplayName>
		<Connector>stackdriver-extension-sample</Connector>
		<Action>log</Action>
		<Input>{
				"logName" : "example-log",
				"metadata" : "test-metadata",
				"message" : "This is a test"
		}</Input>
	<Output>stackdriver-extension-example-log</Output>
</ConnectorCallout>
```

## About the ExtensionCallout policy

Use the ExtensionCallout policy when you want to use a configured extension to access an external resource from within an API proxy.

Before you use this policy, you'll need:

- A few details about the external resource you want to access from this policy. These details will be specific to the resource. For example, if the policy will access your Cloud Firestore database, you'll need to know the collection and document name you want to create or access. You'll typically use resource-specific information in configuring this policy's request and response handling.
- An extension [added, configured, and deployed](/api-platform/extensions/configuring-an-extension)
to the environment where your API proxy will be deployed. In other words,
if you're going to use this policy to access a particular Google Cloud service,
then a deployed extension for that service must exist in your environment.
The configuration details typically include required information for narrowing
access to the resource, such as a project ID or account name.

## Element reference

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<ConnectorCallout async="false" continueOnError="true" enabled="true" name="Extension-Callout-1">
    <DisplayName/>
    <Connector/>
    <Action/>
    <Input/>
    <Output/>
</ConnectorCallout>
```

## \<ConnectorCallout> attributes

```xml
<ConnectorCallout async="false" continueOnError="false" enabled="true" name="Extension-Callout-1">
```

## \<Action> element

The extension-exposed action that the policy should invoke.

```xml
<Action>action-exposed-by-extension</Action>
```

- **Default**: None.
- **Presence**: Required.
- **Type**: String.

Each extension exposes its own set of actions that provide access to functionality of the resource the extension represents. You can think of an action as a function you call with this policy, using the contents of the [`<Input>`](#input_element) element to specify the function's arguments. The action's response is stored in the variable you specify with the [`<Output>`](#output_element) element.

For a list of the extension's functions, see the [reference](/api-platform/reference/extensions/reference-overview-extensions) for the extension you're calling from this policy.

## \<Connector> element

The name of the configured extension to use. This is the environment-scoped name given the extension when it was configured for deployment to an environment.

```xml
<Connector>name-of-configured-extension</Connector>
```

- **Default**: None.
- **Presence**: Required.
- **Type**: String.

An extension has configuration values that might differ from another deployed extension based on the same extension package. These configuration values can represent important differences in runtime functionality between extensions configured from the same package, so be sure to specify the correct extension to invoke.

## \<Input> element

JSON containing the request body to send to the extension. 

```xml
<Input><![CDATA[ JSON-containing-input-values ]]></Input>
```

- **Default**: None.
- **Presence**: Optional or required, depending on the extension.
- **Type**: String.

This is essentially an argument to the action you specify with the [`<Action>`](#action_element) element. The `<Input>` element's value will vary depending on the extension and action you're invoking. See the [extension package documentation](/api-platform/reference/extensions/reference-overview-extensions) for details about each action's properties.

Note that while many `<Input>` element values will function correctly without being enclosed as a `<![CDATA[]]>` section, the rules of JSON allow for values that will not parse as XML. As a best practice, enclose the JSON as a `CDATA` section to avoid runtime parse errors.

The `<Input>` element's value is well-formed JSON whose properties specify values to send to the extension action to invoke. For example, the [Google Stackdriver Logging Extension](/api-platform/reference/extensions/google-stackdriver-logging-extension) extension's `log` action takes values specifying the log to write to (`logName`), metadata to include with the entry (`metadata`), and the log message (`data`). Here's an example:

```xml
<Input><![CDATA[{
    "logName" : "example-log",
    "metadata" : {
        "resource": {
            "type": "global",
            "labels": {
                "project_id": "my-test"
            }
        }
    },
    "message" : "This is a test"
}]]></Input>
```

### Using flow variables in `<Input>` JSON

The content of `<Input>` is treated as a
[message template](/api-platform/reference/message-template-intro). This means that a variable name wrapped in curly braces will be replaced at runtime with the value of the referenced variable.

For example, you could rewrite the preceding `<Input>` block to use the `client.ip` flow variable to get the IP address of the client calling the API proxy:

```xml
<Input><![CDATA[{
    "logName" : "example-log",
    "metadata" : {
        "resource": {
            "type": "global",
            "labels": {
                "project_id": "my-test"
            }
        }
    },
    "message" : "{client.ip}"
}]]></Input>
```

If you intend a property value in the JSON to be enclosed in quotation marks at runtime, be sure to use quotation marks in your JSON code. This is true even when you specify a flow variable as a JSON property value to be resolved at runtime.

The following `<Input>` example includes two flow variable references:

```xml
<Input><![CDATA[{
  "logName" : "example-log",
  "metadata" : {my.log.entry.metadata},
  "message" : "{client.ip}"
}]]></Input>
```

At runtime, JSON property values will resolve as follows:

- `logName` property value -- the string literal `example-log`.
- `metadata` property value -- the `my.log.entry.metadata` flow variable value **without** enclosing quotation marks. This can be useful if the variable's value is itself JSON representing an object.
- `message` property value -- the `client.ip` flow variable value **with** enclosing quotation marks.

## \<Output> element

Name of a variable that stores the extension action's response.

```xml
<Output>variable-name</Output> <!-- The JSON object inside the variable is parsed -->
```
or 

```xml
<Output parsed="false">variable-name</Output>  <!-- The JSON object inside the variable is raw, unparsed -->
```

- **Default**: None.
- **Presence**: Optional or required, depending on the extension.
- **Type**: Parsed object or String, depending on the `parsed` attribute setting.

When the response is received, the response value is placed into the variable you specify here, where you can access it from other API proxy code.

Extension response objects are in JSON format. There are two options for how the policy handles the JSON:

* Parsed (default): The policy parses the JSON object and automatically generates variables with the JSON data. For example, if the JSON contains `"messageId" : 12345;` and you name your output variable  `extensionOutput`, you can access that message ID in other policies using the variable `{extensionOutput.messageId}`.
* Unparsed: The output variable contains the raw, unparsed JSON response from the extension. (If you wanted to, you could still parse the response value in a separate step using the [JavaScript policy](/api-platform/reference/policies/javascript-policy).)

### \<Output> Attributes

Attribute | Description | Default | Presence
-----------|---------------|----------|-----------
parsed | Parses the JSON object returned from the extension, which allows the data in the JSON object to be accessed as variables by other policies. | true | Optional

## Flow variables

None.

## Error codes

Errors returned from Apigee Edge policies follow a consistent format as described in the [Policy error reference](https://docs.apigee.com/api-platform/reference/policies/error-code-reference).</p>

{% dynamic include /includes/___extension-callout-policy-error-codes %}

{% endblock %}
