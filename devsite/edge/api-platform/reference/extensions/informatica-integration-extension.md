{% extends "_base.html" %}
{% block title %}Informatica Integration Cloud Extension{% endblock %}
{% block body %}

{{ extensions_availability }}

Execute Informatica business and integration processes from within an API proxy.

<div class="video-wrapper">
  <iframe class="devsite-embedded-youtube-video" data-video-id="Q43IixBvdO0"
    data-autohide="1" data-showinfo="0" frameborder="0" allowfullscreen>
  </iframe>
</div>

To use this extension, you'll first need to configure it from the extension package installed on Apigee Edge.

This content provides reference for configuring and using this extension. For steps to configure an extension using the Apigee console, see [Adding and configuring an extension](/api-platform/extensions/configuring-an-extension).

## Actions

The Informatica Integration Cloud extension's actions (business and integration processes, in Informatica terms), inputs, outputs, and parameters are generated dynamically based on the processes available to caller.

When the extension is configured by a system administrator, Informatica business and integration processes are retrieved by the extension based on the Informatica credentials used in configuration. These processes are translated into actions and made available to the API proxy developer as actions through the [ExtensionCallout policy](/api-platform/reference/policies/extension-callout-policy).

You can get a list of actions (business and integration processes) available for the Informatica extension's configuration in two ways:

- Use the management API to retrieve a list of actions after the extension is configured. In the following URL, `id` is the unique identifier assigned to the extension when it was added.

    ```
    curl -H "Authorization: Bearer $USER_TOKEN" "https://$PROXY_DOMAIN/organizations/my-org/environments/my-env/extensions/{id}/actions"
    ```
- View the list of actions displayed when you add a ExtensionCallout policy that references the Informatica Integration Cloud extension.

    When you add the policy, you're prompted to select an action. That list of actions is generated from Informatica business and integration processes discovered at configuration.

    After you choose an action and add the policy, the policy's configuration XML includes schema describing the action's inputs, outputs, and parameters (if any). In the following example, the action includes two input parameters, Message and EmailAddress, and no output.

    ```xml
    <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
    <ConnectorCallout async="false" continueOnError="true" enabled="true" name="My-Informatica-Extension">
        <DisplayName>Send Email</DisplayName>
        <Connector>configured-informatica-extension</Connector>
        <Action>/2PIlv0QbOsxe8u8QieZnIF/Send_Email-1/Send_Email-1</Action>
        <Input></Input>
    </ConnectorCallout><!-- 
    Input JSON Schema for package=informatica-integration version=0.0.1
    {"type":"object","properties":{"Message":{"type":"string"},"EmailAddress":{"type":"string"}}}
    -->
    ```

    For the preceding example, you'd configure the `<Input>` as follows:

    ```xml
    <Input><Input><![CDATA[{
        "Message": "An email message to send.",
        "EmailAddress": "anaddress@example.com"
    }]]></Input>
    ```

## Configuration Reference

Use the following when you're configuring and deploying this extension for use in API proxies.

Note: The ability to configure and deploy extensions is available only to system administrators.

### Common extension properties

{% dynamic include /includes/___common-extension-properties %}

### Properties for this extension package

Specify values for the following configuration properties specific to this extension.

Property | Description | Default | Required
-------- | ----------- | ------- | --------
credentials | JSON specifying the user name and password for the Informatica account. For example:<pre>{ "username": "my-informatica-username", "password": "my-password" } </pre> | None. | Yes.

{% endblock %}

