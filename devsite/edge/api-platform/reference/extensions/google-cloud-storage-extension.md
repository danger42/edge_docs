{% extends "_base.html" %}
{% block title %}Google Cloud Storage Extension{% endblock %}
{% block body %}

{{ extensions_availability }}

List, download, and generate signed URLs for files in a [Cloud Storage](https://cloud.google.com/storage/) bucket.

This content provides reference for configuring and using this extension. Before using this extension from an API proxy, you must:

1. [Create a Cloud Storage bucket](https://cloud.google.com/storage/docs/creating-buckets).

1. [Upload objects](https://cloud.google.com/storage/docs/uploading-objects) to your bucket.

1. [Grant permission](https://cloud.google.com/iam/docs/granting-roles-to-service-accounts) for accessing the bucket to the GCP service account that represents your Google Cloud Storage extension. For more on the role to use, see [Cloud Storage roles](https://cloud.google.com/storage/docs/access-control/iam-roles). For more on permissions in Cloud Storage, see [Using Identity and Access Management (IAM) Permissions](https://cloud.google.com/storage/docs/access-control/using-iam-permissions).

1. [Generate a key for the service account](https://cloud.google.com/iam/docs/creating-managing-service-account-keys).

1. Use the contents of the resulting key JSON file when [adding and configuring](/api-platform/extensions/configuring-an-extension) the extension using the [configuration reference](#configuration_reference).

## About Cloud Storage

[Cloud Storage](https://cloud.google.com/storage/docs/) is a service for secure, durable, and scalable file storage. If you're just getting started with Cloud Storage, the [quickstart in the Cloud Storage documentation](https://cloud.google.com/storage/docs/quickstart-console) is a good place to begin.

## Samples

The following examples illustrate how to configure support for Cloud Storage extension actions using the [ExtensionCallout policy](/api-platform/reference/policies/extension-callout-policy).

  * {List files}

    In the following example, the extension's `listFiles` action retrieves a list of the files, returning them in an array. The `listFiles` action takes no input parameters.

    ```xml
    <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
    <ConnectorCallout async="false" continueOnError="true" enabled="true" name="Get-Storage-File-List">
        <DisplayName>Get Storage File List</DisplayName>
        <Connector>cloud-storage-extension-example</Connector>
        <Action>listFiles</Action>
        <Input><![CDATA[{}]]></Input>
        <Output>storage.filelist.retrieved</Output>
    </ConnectorCallout>
    ```

    The response value looks something like this:

    ```json
    ["example-text.txt","example-image.png"]
    ```

    The following Assign Message policy retrieves the response value from the ExtensionCallout policy above and copies it in the response payload.

    ```xml
    <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
    <AssignMessage async="false" continueOnError="false" enabled="true" name="Assign-Storage-File-List">
        <DisplayName>Assign Storage File List</DisplayName>
        <AssignTo type="response" createNew="false"/>
        <Set>
            <Payload contentType="application/json">{storage.filelist.retrieved}</Payload>
        </Set>
    </AssignMessage>
    ```
  * {Download a file}

    The following ExtensionCallout policy uses the Google Cloud Storage extension to download a simple text file whose contents are simply `Some example text.`,

    ```xml
    <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
    <ConnectorCallout async="false" continueOnError="true" enabled="true" name="Download-File">
        <DisplayName>Download File</DisplayName>
        <Connector>cloud-storage-extension-example</Connector>
        <Action>downloadFile</Action>
        <Input><![CDATA[{"fileName": "example-text.txt"}]]></Input>
        <Output>storage.file.retrieved</Output>
    </ConnectorCallout>
    ```

    The response value looks like this:

    ```json
    {"content":"Some example text."}
    ```

    The following Assign Message policy retrieves the value returned by the extension and copies it into the response payload.

    ```xml
    <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
    <AssignMessage async="false" continueOnError="false" enabled="true" name="Assign-Storage-File-List">
        <DisplayName>Assign Storage File List</DisplayName>
        <AssignTo type="response" createNew="false"/>
        <Set>
            <Payload contentType="application/json">{storage.file.retrieved}</Payload>
        </Set>
    </AssignMessage>
    ```

  * {Generate a signed URL}

    The following ExtensionCallout policy uses this extension to generate a URL for downloading the specified file from the Cloud Storage bucket. It passes the name of the file, along with the date after which the URL should expire and no longer function.

    ```xml
    <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
    <ConnectorCallout async="false" continueOnError="true" enabled="true" name="Get-Signed-URL">
        <DisplayName>Get Signed URL</DisplayName>
        <Connector>cloud-storage-extension-example</Connector>
        <Action>generateSignedURL</Action>
        <Input><![CDATA[{
            "fileName" : "example-text.txt",
            "expiresOn" : "2018-08-05"
        }]]></Input>
        <Output>storage.url.retrieved</Output>
    </ConnectorCallout>
    ```

    The extension's return value looks something like the following.

    ```json
    {"url":"https://storage.googleapis.com/storage-extension-example/example-text.txt?GoogleAccessId=extension-test%40my-test-33333.iam.gserviceaccount.com&Expires=1533427200&Signature=Y1cE1DCHesWeIZILRhdIuDR%2FhzZXZ%2BPeY3J1PUkRiosFYj41itHBWh2%2BTQgH9kI6E8s2mWrVDFU43YR7s8Tm9W5VgWRwh0nXSactQ0xKbkKbGZmCcWxgIscOezc1zc%2Bp7lnXSx1qd4wIlIKVH4KCd9WLx4qB1dLxGNxMKB32tA3dio5IiMXaHEA%2FR2fYc0Pjh45t8L5rilk5pekv7jfd3sfsgdfgfdglkj%2F7E%2FlJ%2B60RnetqV2IDqrc0sVEgSLTpgTbDGU%2Ft3EcitRUFOSdOb5czt7CiIwKAYSmDEFMSNHHiNTWjvLzq4IU%2BCa4Z5aKyvww%3D%3D"}
    ```

    The following Assign Message policy retrieves the value returned by the extension and copies it into the response body.

    ```xml
    <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
    <AssignMessage async="false" continueOnError="false" enabled="true" name="Assign-Storage-File-URL">
        <DisplayName>Assign Storage File URL</DisplayName>
        <AssignTo type="response" createNew="false"/>
        <Set>
            <Payload contentType="application/json">{storage.url.retrieved}</Payload>
        </Set>
    </AssignMessage>
    ```

## Actions

### downloadFile

Downloads the specified file.

#### Request parameters

Parameter | Description | Type | Default | Required
--------- | ----------- | ---- | ------- | --------
fileName | The file to download. | String | None. | Yes.

  * {Syntax}

    ```xml
    <Input><![CDATA[{"fileName" : "the-file-to-download"}]]></Input>
    ```

  * {Example}

    ```xml
    <Input><![CDATA[{"fileName" : "example-text.txt"}]]></Input>
    ```

#### Response

JSON representing an object with the content of the downloaded file. For example:

```json
{"content":"Some example text."}
```

### listFiles

Lists files available for download.

#### Request parameters

None.

#### Response

An array of file names. For example:

```json
["example-text.txt","example-image.png"]
```

### generateSignedURL

Creates a signed URL for a the specified file in the bucket. This URL can be used to download the file.

#### Request parameters

Parameter | Description | Type | Default | Required
--------- | ----------- | ---- | ------- | --------
fileName | Name of the Cloud Storage object for which to generate a signed URL. | String | None. | Yes.
expiresOn | Date on which the signed URL should expire. | String | None. | No.

  * {Syntax}

    ```xml
    <Input><![CDATA[{
      "fileName" : "file-for-which-to-generate-url",
      "expiresOn" : "date-to-expire-url"
    }]]></Input>
    ```

  * {Example}

    ```xml
    <Input><![CDATA[{
      "fileName" : "example-text.txt",
      "expiresOn" : "2018-08-05"
    }]]></Input>
    ```

#### Response

JSON representing an object with the signed URL. For example:

```json
{"url":"https://storage.googleapis.com/storage-extension-example/example-text.txt?GoogleAccessId=extension-test%40my-test-33333.iam.gserviceaccount.com&Expires=1533427200&Signature=Y1cE1DCHesWeIZILRhdIuDR%2FhzZXZ%2BPeY3J1PUkRiosFYj41itHBWh2%2BTQgH9kI6E8s2mWrVDFU43YR7s8Tm9W5VgWRwh0nXSactQ0xKbkKbGZmCcWxgIscOezc1zc%2Bp7lnXSx1qd4wIlIKVH4KCd9WLx4qB1dLxGNxMKB32tA3dio5IiMXaHEA%2FR2fYc0Pjh45t8L5rilk5pekv7jfd3sfsgdfgfdglkj%2F7E%2FlJ%2B60RnetqV2IDqrc0sVEgSLTpgTbDGU%2Ft3EcitRUFOSdOb5czt7CiIwKAYSmDEFMSNHHiNTWjvLzq4IU%2BCa4Z5aKyvww%3D%3D"}
```

## Configuration Reference

Use the following when you're configuring and deploying this extension for use in API proxies. For steps to configure an extension using the Apigee console, see [Adding and configuring an extension](/api-platform/extensions/configuring-an-extension).

Note: The ability to configure and deploy extensions is available only to system administrators.

### Common extension properties

{% dynamic include /includes/___common-extension-properties %}

### Properties for this extension package

Property | Description | Default | Required
-------- | ----------- | ------- | --------
bucketName | The GCS bucket with which this extension should interact. | None. | Yes.

{% endblock %}
