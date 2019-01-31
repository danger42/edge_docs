{% extends "_base.html" %}
{% block title %}Google Cloud Pub/Sub Extension{% endblock %}
{% block body %} 

{{ extensions_availability }}

> Version: 1.2.0

Publish messages to a topic in the Google Cloud Pub/Sub service.

This content provides reference for configuring and using this extension. 

## Prerequisites

Before using this extension from an API proxy, you must:

1. Create a Cloud Pub/Sub topic, as described in [Quickstart: Using the console](https://cloud.google.com/pubsub/docs/quickstart-console).

1. Once you have the topic, [grant permission](https://cloud.google.com/iam/docs/granting-roles-to-service-accounts) to publish to your Cloud Pub/Sub topic. For example, you can use the `roles/pubsub.publisher` role to publish to topics. For more on Cloud Pub/Sub roles, see [Access Control](https://cloud.google.com/pubsub/docs/access-control).

1. [Generate a key for the service account](https://cloud.google.com/iam/docs/creating-managing-service-account-keys). 

1. Use the contents of the resulting key JSON file when [adding and configuring](/api-platform/extensions/configuring-an-extension) the extension using the [configuration reference](#configuration_reference).

## About Cloud Pub/Sub

[Cloud Pub/Sub](https://cloud.google.com/pubsub/docs/overview) provides many-to-many, asynchronous messaging that decouples senders and receivers. Cloud Pub/Sub delivers low-latency, durable messaging that helps developers quickly integrate systems hosted on the Google Cloud Platform and externally.

If you're just getting started with Cloud Pub/Sub, take a look at the [quickstarts in the Cloud Pub/Sub documentation](https://cloud.google.com/pubsub/docs/quickstarts).

## Samples

The following examples illustrate how to configure support for Cloud Pub/Sub extension actions using the [ExtensionCallout policy](/api-platform/reference/policies/extension-callout-policy).

  * {Publish a message}

    In this example, the `publish` action publishes a simple message to a topic specified in the extension's [configuration](#configuration_reference). The `publish` action returns the ID of the published message in the topic to which it was published.

    ```xml
    <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
    <ConnectorCallout async="false" continueOnError="true" enabled="true" name="Publish-Message">
        <DisplayName>Publish Message</DisplayName>
        <Connector>cloud-pub-sub-extension-example</Connector>
        <Action>publish</Action>
        <Input><![CDATA[
          {
              "message" : "My test message."
          }
        ]]></Input>
        <Output>topic.message.id</Output>
    </ConnectorCallout>
    ```

    The following [Assign Message](/api-platform/reference/policies/assign-message-policy) policy uses the value of the variable storing the extension's response to assign the response payload.

    ```xml
    <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
    <AssignMessage async="false" continueOnError="false" enabled="true" name="Get-Topic-Message-ID">
        <DisplayName>Get Topic Message ID</DisplayName>
        <AssignTo type="response" createNew="false"/>
        <Set>
            <Payload contentType="application/json">{topic.message.id}</Payload>
        </Set>
    </AssignMessage>
    ```

    In this example, the response data is returned as JSON such as the following.

    ```json
    {"messageId":"165918654986954"}

    ```

## Actions

### publish

Publishes a message to the topic specified in this extension's [configuration](#configuration_reference).

* {Syntax}

  ```xml
  <Action>publish</Action>
  <Input><![CDATA[{
    "message" : "message-to-publish"
  }]]></Input>
  ```

* {Example}

  ```xml
  <Action>publish</Action>
  <Input><![CDATA[
    {
        "message" : "My test message."
    }
  ]]></Input>
  ```

#### Request parameters

Parameter | Description | Type | Default | Required
--------- | ----------- | ---- | ------- | --------
message | The message to publish. | String | None. | Yes.

#### Response

JSON containing the ID of the message as published to the topic. For example:

```json
{"messageId":"165918654986954"}
```

## Configuration Reference

Use the following when you're configuring and deploying this extension for use in API proxies.

Note: The ability to configure and deploy extensions is available only to system administrators.

### Common extension properties

{% dynamic include /includes/___common-extension-properties %}

### Properties for this extension package

Property | Description | Default | Required
-------- | ----------- | ------- | --------
topic | The topic to publish to. Get this value from the Pub/Sub service, such as from the GCP console for the service. For example, the topic name might take the form `projects/GCP-PROJECT-ID/topics/TOPIC-NAME` | None. | Yes.

{% endblock %}
