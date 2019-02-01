{% extends "_base.html" %}
{% block title %}Google Cloud Data Loss Prevention Extension{% endblock %}
{% block body %}

{{ extensions_availability }}

Use this extension to obscure sensitive data from content and images. For example, you might redact credit card numbers, names, and social security numbers.

This content provides reference for configuring and using this extension. For this extension's configuration reference, see [Configuration Reference](#configuration_reference).

## Prerequisites

This content provides reference for configuring and using this extension. Before using the extension from an API proxy using the {{ extension_callout_policy }}, you must:

1. [Enable the Google Cloud DLP API](https://cloud.google.com/dlp/docs/quickstart-cli) for your project.

1. [Grant permission](https://cloud.google.com/iam/docs/creating-managing-service-accounts#required_permissions) for the level of access you'll want for the extension.

1. [Generate a key for the service account](https://cloud.google.com/iam/docs/creating-managing-service-accounts#creating_a_service_account).

1. Use the contents of the resulting key JSON file when [adding and configuring](/api-platform/extensions/configuring-an-extension) the extension using the [configuration reference](#configuration_reference).

## About Cloud Data Loss Prevention (DLP)

[Cloud Data Loss Prevention (DLP)](https://cloud.google.com/dlp/docs/) is an API for inspecting text, images, and other data to identify and manage sensitive data.

For more, see the [DLP overview](https://cloud.google.com/dlp/). For reference to the API that this extension exposes, see [Cloud Data Loss Prevention (DLP) API](https://cloud.google.com/dlp/docs/reference/rest/).

## Samples

The following examples illustrate how to configure support for Cloud Spanner extension actions using the [ExtensionCallout policy](/api-platform/reference/policies/extension-callout-policy).

To make trying this sample code easier, these examples use an [AssignMessage policy](/api-platform/reference/policies/assign-message-policy) to set flow variable values and to retrieve extension response values for display in the [Trace tool](/api-platform/debug/using-trace-tool-0).

  * {Mask with stars}

    This example uses the `deidentifyWithMask` action to mask the specified types of text with a character specified in the policy -- here, the `*` character.

    The following AssignMessage policy sets the [`request.content`](/api-platform/reference/variables-reference#request) variable for illustration purposes. Ordinarily, you'd retrieve the request content from the client's request.

    ```xml
    <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
    <AssignMessage async="false" continueOnError="false" enabled="true" name="Set-Variable">
        <DisplayName>Set Variable</DisplayName>
        <AssignTo type="response" createNew="false"/>
        <AssignVariable>
            <Name>request.content</Name>
            <Value>Visit my site at https://example.com. Or contact me at gladys@example.com.</Value>
        </AssignVariable>
    </AssignMessage>
    ```

    The following [ExtensionCallout policy](/api-platform/reference/policies/extension-callout-policy) retrieves the request.content variable value and passes it to a Cloud DLP extension (here, called `example-dlp`). That extension has been configured to mask values based on the `URL` and `EMAIL_ADDRESS` [infoTypes](https://cloud.google.com/dlp/docs/infotypes-reference).

    ```xml
    <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
    <ConnectorCallout async="false" continueOnError="true" enabled="true" name="Data-Loss-Extension-Callout">
        <DisplayName>Data Loss Prevention Extension Callout</DisplayName>
        <Connector>example-dlp</Connector>
        <Action>deidentifyWithMask</Action>
        <Input><![CDATA[{
            "text" : "{request.content}",
            "mask" : "*"
        }]]></Input>
        <Output>masked.output</Output>
    </ConnectorCallout>
    ```

    The following AssignMessage policy retrieves the extension's output for display in the Trace tool.

    ```xml
    <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
    <AssignMessage async="false" continueOnError="false" enabled="true" name="Get-DLP-Output">
        <DisplayName>Get DLP Output</DisplayName>
        <AssignTo type="response" createNew="false"/>
        <Set>
            <Payload contentType="application/json">{masked.output}</Payload>
        </Set>
    </AssignMessage>
    ```

    The following is an example of output from this code.

    ```json
    {"text":"Visit my site at ******************* Or contact me at *****************."}
    ```

  * {Mask with name}

    This example uses the `deidentifyWithType` action to mask the specified types of text with the infotype name itself. For example, it would replace the email address `gladys@example.com` with `EMAIL_ADDRESS`.

    The following AssignMessage policy sets the [`request.content`](/api-platform/reference/variables-reference#request) variable for illustration purposes. Ordinarily, you'd retrieve the request content from the client's request.

    ```xml
    <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
    <AssignMessage async="false" continueOnError="false" enabled="true" name="Set-Variable">
        <DisplayName>Set Variable</DisplayName>
        <AssignTo type="response" createNew="false"/>
        <AssignVariable>
            <Name>request.content</Name>
            <Value>Visit my site at https://example.com. Or contact me at gladys@example.com.</Value>
        </AssignVariable>
    </AssignMessage>
    ```

    The following [ExtensionCallout policy](/api-platform/reference/policies/extension-callout-policy) retrieves the `request.content` variable value and passes it to a Cloud DLP extension (here, called `example-dlp`). That extension has been configured to mask values based on the `URL` and `EMAIL_ADDRESS` [infoTypes](https://cloud.google.com/dlp/docs/infotypes-reference).

    ```xml
    <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
    <ConnectorCallout async="false" continueOnError="true" enabled="true" name="Data-Loss-Extension-Callout">
        <DisplayName>Data Loss Prevention Extension Callout</DisplayName>
        <Connector>example-dlp</Connector>
        <Action>deidentifyWithType</Action>
        <Input><![CDATA[{
            "text" : "{request.content}"
        }]]></Input>
        <Output>masked.output</Output>
    </ConnectorCallout>
    ```

    The following AssignMessage policy retrieves the extension's output for display in the Trace tool.

    ```xml
    <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
    <AssignMessage async="false" continueOnError="false" enabled="true" name="Get-DLP-Output">
        <DisplayName>Get DLP Output</DisplayName>
        <AssignTo type="response" createNew="false"/>
        <Set>
            <Payload contentType="application/json">{masked.output}</Payload>
        </Set>
    </AssignMessage>
    ```

    The following is an example of output from this code.

    ```json
    {"text":"Visit my site at [URL] Or contact me at [EMAIL_ADDRESS]."}
    ```

## Actions

### deidentifyWithMask

De-identify the sensitive data from `text` and mask the data with the `mask` character. This action masks those parts of `text` specified by the `infoTypes` property in [extension configuration](#configuration_reference).

Masking sensitive data replaces characters with a symbol, such as an asterisk (\*) or hash (#). The  types of the sensitive data can be configured in extension configuration.

* {Syntax}

  ```xml
  <Action>deidentifyWithMask</Action>
  <Input><![CDATA[{
    "text" : "text-to-deidentify",
    "mask" : "masking-character"
  }]]></Input>
  ```

* {Example}

  In the following example, the input to mask is an email address stored in an `input.email.address` flow variable. To support this specific example, this extension must have been configured to support the EMAIL_ADDRESS infoType. For a list of infoTypes, see [InfoType Detector Reference](https://cloud.google.com/dlp/docs/infotypes-reference).

  ```xml
  <Action>deidentifyWithMask</Action>
  <Input><![CDATA[{
      "text" : "{input.email.address}",
      "mask" : "*"
  }]]></Input>
  <Output>masked.output</Output>
  ```

  The output for this example would be the following:

  ```json
  {"text":"*****************"}
  ```

#### Request parameters

Parameter | Description | Type | Default | Required
--------- | ----------- | ---- | ------- | --------
text | Text to de-identify. | String | None. | Yes.
mask | Character to use to mask sensitive data. | String | None. | Yes.

#### Response

The input text with values of the specified infoTypes replaced with the specified character. For example,

```json
{"text":"*********"}
```

### deidentifyWithType

De-identify sensitive data in text content, replacing each matched value with the name of the infoType. For a list of infoTypes, see [InfoType detector reference](https://cloud.google.com/dlp/docs/infotypes-reference). This action masks those parts of `text` specified by the `infoTypes` property in [extension configuration](#configuration_reference).

In the following example, the phone number is recognized by the service, then replaced with the name of the infoType itself.

- Input text:

    `John Smith, 123 Main St, Seattle, WA 98122, 206-555-0123.`

- Result text:

    `John Smith, 123 Main St, Seattle, WA 98122, PHONE_NUMBER.`

* {Syntax}

  ```xml
  <Action>deidentifyWithType</Action>
  <Input><![CDATA[{
    "text" : "text-to-deidentify"
  }]]></Input>
  ```
  
* {Example}

  In the following example, the input to mask is an email address stored in an `input.email.address` flow variable. To support this specific example, this extension must have been configured to support the EMAIL_ADDRESS infoType. For a list of infoTypes, see [InfoType Detector Reference](https://cloud.google.com/dlp/docs/infotypes-reference).

  ```xml
  <Action>deidentifyWithType</Action>
  <Input><![CDATA[{
      "text" : "{input.email.address}"
  }]]></Input>
  ```

  The output for this example would be the following:

  ```json
  {"text":"EMAIL_ADDRESS"}
  ```

#### Request parameters

Parameter | Description | Type | Default | Required
--------- | ----------- | ---- | ------- | --------
text | The text to de-identify. | String | None. | Yes.

#### Response

The input text with values of the specified infoTypes replaced with the infoType names. For example,

```json
{"text":"EMAIL_ADDRESS"}
```

### redactImage

Redact text that falls into one of the infoType categories. The redacted content is detected and obscured with an opaque rectangle. This action masks those parts of `image_data` specified by the `infoTypes` property in [extension configuration](#configuration_reference).

For a list of infoTypes, see [InfoType detector reference](https://cloud.google.com/dlp/docs/infotypes-reference).

#### Request parameters

```xml
<Action>redactImage</Action>
<Input><![CDATA[{
  "image_data" : "base64-encoded-image-to-analyze",
  "image_type" : "type-of-image"
}]]></Input>
```

Parameter | Description | Type | Default | Required
--------- | ----------- | ---- | ------- | --------
image_data | The image data encoded in base64. | String | None. | Yes.
image_type | Constant of the image type. Available values are IMAGE_JPEG, IMAGE_BMP, IMAGE_PNG, IMAGE_SVG. | String | None. | Yes.

#### Response

The image with text redacted.

## Configuration Reference

Use the following when you're configuring and deploying this extension for use in API proxies. For steps to configure an extension using the Apigee console, see [Adding and configuring an extension](/api-platform/extensions/configuring-an-extension).

Note: The ability to configure and deploy extensions is available only to system administrators.

### Common extension properties

{% dynamic include /includes/___common-extension-properties %}

### Properties for this extension package

Specify values for the following configuration properties specific to this extension.

Property | Description | Default | Required
-------- | ----------- | ------- | --------
projectId | The GCP project ID for which the Cloud Data Loss Prevention API is enabled. | None. | Yes.
infoTypes | Info types of the sensitive data. If omitted, the service will detect all built-in types. For a list of infoTypes supported by the Google Cloud DLP service, see [InfoType Detector Reference](https://cloud.google.com/dlp/docs/infotypes-reference). | None. | No.
credentials | When entered in the Apigee Edge console, this is the contents of your [service account key file](https://cloud.google.com/iam/docs/creating-managing-service-account-keys). When sent via the management API, it is a base64-encoded value generated from the service account key file. | None. | Yes.

{% endblock %}
