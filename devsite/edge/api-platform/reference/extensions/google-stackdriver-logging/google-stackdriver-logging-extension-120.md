{% extends "_base.html" %}
{% block title %}Google Stackdriver Logging Extension{% endblock %}
{% block body %} 

{{ extensions_availability }}

> Version 1.2.0

Write entries to [Stackdriver Logging](https://cloud.google.com/logging/) logs.

This content provides reference for configuring and using this extension. 

## Prerequisites

Before using this extension from an API proxy, you must:

1. In IAM, assign the Logging > Logs Writer permission to the project member that will represent your extension to the Stackdriver Logging system. For instructions on how to grant roles, see [Granting roles to a service account for specific resources](https://cloud.google.com/iam/docs/granting-roles-to-service-accounts#granting_access_to_a_service_account_for_a_resource).
  For more on logging roles, see [Access Control Guide](https://cloud.google.com/logging/docs/access-control).

1. [Generate a key for the service account](https://cloud.google.com/iam/docs/creating-managing-service-account-keys).

1. Use the contents of the resulting key JSON file when [adding and configuring](/api-platform/extensions/configuring-an-extension) the extension using the [configuration reference](#configuration_reference).

## About Stackdriver Logging

[Stackdriver Logging](https://cloud.google.com/logging/) is part of the Stackdriver suite of products in Google Cloud Platform (GCP). It includes storage for logs, a user interface called the Logs Viewer, and an API to manage logs programmatically. With Stackdriver Logging, you can read and write log entries, search and filter your logs, export your logs, and create logs-based metrics.

This extension currently writes entries to the log.

For more, see the [Stackdriver Logging docs](https://cloud.google.com/logging/docs/).

## Samples

The following examples illustrate how to configure support for Stackdriver Logging extension actions using the [ExtensionCallout policy](/api-platform/reference/policies/extension-callout-policy).

### Global log

The following policy writes the message "This is a test" to a log called example-log in the Stackdriver Global resource. In actual use, you'd probably have the message be contained in a flow variable whose value you set elsewhere in the API proxy.

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<ConnectorCallout async="false" continueOnError="true" enabled="true" name="Logging-Extension">
    <DisplayName>Logging Connector</DisplayName>
    <Connector>stackdriver-extension-sample</Connector>
    <Action>log</Action>
    <Input><![CDATA[{
        "logName": "example-log",
        "metadata": {
            "resource": {
                "type": "global",
                "labels": {
                    "project_id": "my-test"
                }
            }
        },
        "message": "This is a test"
    }]]></Input>
</ConnectorCallout>
```

This content provides reference for configuring and using this extension. For steps to configure an extension using the Apigee console, see [Adding and configuring an extension](/api-platform/extensions/configuring-an-extension).

## Actions

### log

Writes a message to the log.

This action writes a Stackdriver log entry. Log entries consist of metadata and the entry data. For more about log entries, see the [Entry reference](https://cloud.google.com/nodejs/docs/reference/logging/1.2.x/Entry). For information about contents of the `metadata` property, see the [MonitoredResource](https://cloud.google.com/logging/docs/reference/v2/rest/v2/MonitoredResource) object in the Stackdriver Logging documentation.

* {Syntax}

  ```xml
  <Action>log</Action>
  <Input><![CDATA[{
    "logName" : "stackdriver-log-name-to-use",
    "metadata" : JSON-structured-metadata,
    "message" : "data-to-log-as-entry"
  }]]></Input>
  ```

* {Example}

  ```xml
  <Action>log</Action>
  <Input><![CDATA[{
    "logName" : "stackdriver-log-name-to-use",
    "metadata" : { "resource" : { "type" : "global" } },
    "message" : "data-to-log-as-entry"
  }]]></Input>
  ```

#### Request parameters

Parameter | Description | Type | Default | Required
--------- | ----------- | ---- | ------- | --------
logName | The name of the log to which this entry belongs. | String | None. | Yes.
metadata | Metadata about the log entry.<br/>For information about contents of the `metadata` `resource` property, see the [MonitoredResource](https://cloud.google.com/logging/docs/reference/v2/rest/v2/MonitoredResource) object in the Stackdriver Logging documentation. | JSON | None. | No.
message | Data to use as the value for this log entry. | String | None. | Yes.

#### Response

None if the message was written to the log. Otherwise, the request will return an error. See also [Debugging an extension](/api-platform/extensions/debugging-an-extension).

## Configuration Reference

Use the following when you're configuring and deploying this extension for use in API proxies.

Note: The ability to configure and deploy extensions is available only to system administrators.

### Common extension properties

{% dynamic include /includes/___common-extension-properties %}

### Properties for this extension package

Specify values for the following configuration properties specific to this extension.

Property | Description | Default | Required
-------- | ----------- | ------- | --------
projectId | The GCP project ID where logs should be created. | None. | Yes.
credentials | When entered in the Apigee Edge console, this is the contents of your [service account key file](https://cloud.google.com/iam/docs/creating-managing-service-account-keys). When sent via the management API, it is a base64-encoded value generated from the service account key file. | None. | Yes.

{% endblock %}
