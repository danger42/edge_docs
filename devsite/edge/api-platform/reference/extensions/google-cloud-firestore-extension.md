{% extends "_base.html" %}
{% block title %}Google Cloud Firestore Extension{% endblock %}
{% block body %}

{{ extensions_availability }}

Create, read, or delete data in a Cloud Firestore database.

This content provides reference for configuring and using this extension. Before using this extension from an API proxy, you must:

1. Create a Firebase project at the [Firebase console](https://firebase.corp.google.com/) where your data is stored.

1. [Generate a key for the service account](https://firebase.google.com/docs/admin/setup#add_firebase_to_your_app).

1. Use the contents of the resulting key JSON file when [adding and configuring](/api-platform/extensions/configuring-an-extension) the extension using the [configuration reference](#configuration_reference).

## About Cloud Firestore

[Cloud Firestore](https://firebase.google.com/docs/firestore/) stores data in documents, which are stored in collections. Cloud Firestore creates collections and documents implicitly the first time you add data to the document. You do not need to explicitly create collections or documents.

For more about Cloud Firestore generally, see the [Get started with Firestore](https://firebase.google.com/docs/firestore/quickstart) in the Cloud Firestore documentation.

Note: Cloud Firestore is currently in beta release. Features and support will continue to improve as the product matures.

## Samples

The following examples illustrate how to configure support for Cloud Firestore extension actions using the [ExtensionCallout policy](/api-platform/reference/policies/extension-callout-policy).

  * {Add data}

    The following [ExtensionCallout policy](/api-platform/reference/policies/extension-callout-policy) adds a document called `freewill@example.com` to a `users` collection. The `data` property specifies the fields and values of the new document.

    ```xml
    <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
    <ConnectorCallout async="false" continueOnError="true" enabled="true" name="Add-User-Data">
        <DisplayName>Add User Data</DisplayName>
        <Connector>my-cloud-firestore-extension</Connector>
        <Action>setDocument</Action>
        <Input><![CDATA[{
            "colName" : "users",
            "docName" : "freewill@example.com",
            "data" : {
                "firstName": "Will",
                "lastName": "Witman",
                "address": "270-8243 Tempor St.",
                "city": "Fort Worth",
                "region": "TX",
                "postalCode": "86519",
                "email": "freewill@example.com",
                "username": "freewill444"
            }
        }]]></Input>
    </ConnectorCallout>
    ```

  * {Get data}

    In this example, the [ExtensionCallout policy](/api-platform/reference/policies/extension-callout-policy) retrieves the value of the `freewill@example.com` document from the `users` collection. Here, the `<Output>` element's `parsed` attribute is set to `false` so that the returned result will be JSON as a string, rather than JSON parsed into an object. For more, see the [`<Output>` element reference](/api-platform/reference/policies/extension-callout-policy#output_element).

    ```xml
    <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
    <ConnectorCallout async="false" continueOnError="true" enabled="true" name="Get-User-Data">
        <DisplayName>Get User Data</DisplayName>
        <Connector>straut-cloud-firestore-extension</Connector>
        <Action>getDocument</Action>
        <Input><![CDATA[{
            "colName" : "users",
            "docName" : "freewill@example.com"
        }]]></Input>
        <Output parsed="false">firestore.userdata.retrieved</Output>
    </ConnectorCallout>
    ```

    The following [Assign Message](/api-platform/reference/policies/assign-message-policy) policy uses the value of the variable storing the extension's response to assign the response payload.

    ```xml
    <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
    <AssignMessage name="CopyUserDataToResponse">
        <DisplayName>Copy User Data To Response</DisplayName>
        <AssignTo type="response" createNew="false"/>
        <Set>
            <Payload contentType="application/json">{firestore.userdata.retrieved}</Payload>
        </Set>
    </AssignMessage>
    ```

  * {Delete data}

    In this example, the [ExtensionCallout policy](/api-platform/reference/policies/extension-callout-policy) deletes the `lizzie@example.com` document from the `users` collection.

    ```xml
    <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
    <ConnectorCallout async="false" continueOnError="true" enabled="true" name="Delete-User-Data">
        <DisplayName>Delete User Data</DisplayName>
        <Connector>my-cloud-firestore-extension</Connector>
        <Action>deleteDocument</Action>
        <Input><![CDATA[{
            "colName" : "users",
            "docName" : "lizzie@example.com"
        }]]></Input>
    </ConnectorCallout>
    ```

  * {Query data}

    In this example, the [ExtensionCallout policy](/api-platform/reference/policies/extension-callout-policy) queries a `cities` collection. Query results are filtered by the `state` and `population` fields. Here, the `<Output>` element's `parsed` attribute is set to `false` so that the returned result will be JSON as a string, rather than JSON parsed into an object. For more, see the [`<Output>` element reference](/api-platform/reference/policies/extension-callout-policy#output_element).

    ```xml
    <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
    <ConnectorCallout async="false" continueOnError="true" enabled="true" name="Query-City-Data">
        <DisplayName>Query City Data</DisplayName>
        <Connector>cloud-firestore-extension</Connector>
        <Action>query</Action>
        <Input><![CDATA[{
            "colName":"cities",
            "queryArray":[
              ["state", "==", "CA"],
              ["population","<",1000000]
            ]
        }]]></Input>
        <Output parsed="false">compound-query-output</Output>
    </ConnectorCallout>
    ```

    The following [Assign Message](/api-platform/reference/policies/assign-message-policy) policy uses the value of the variable storing the extension's response to assign the response payload.

    ```xml
    <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
    <AssignMessage name="CopyQueryResultsToResponse">
        <DisplayName>Copy Query Results To Response</DisplayName>
        <AssignTo type="response" createNew="false"/>
        <Set>
            <Payload contentType="application/json">{firestore.querydata.retrieved}</Payload>
        </Set>
    </AssignMessage>
    ```


## Actions

### deleteDocument

Deletes a single document from a collection.

#### Request parameters

```xml
<Input><![CDATA[{
  "colName" : "firestore-collection-name",
  "docName" : "firestore-document-name"
}]]></Input>
```

Parameter | Description | Type | Default | Required
--------- | ----------- | ---- | ------- | --------
colName | Name of the collection containing the document to delete. | String | None. | Yes.
docName | Name of the document to delete. | String | None. | Yes.

#### Response

None.

### getDocument

Retrieves the contents of a single document.

#### Request parameters

```xml
<Input><![CDATA[{
  "colName" : "firestore-collection-name",
  "docName" : "firestore-document-name"
}]]></Input>
```

Parameter | Description | Type | Default | Required
--------- | ----------- | ---- | ------- | --------
colName | Name of the collection from which to retrieve the document. | String | None. | Yes.
docName | Name of the document to retrieve. | String | None. | Yes.

#### Response

Object containing the content of the document in JSON format.

### query

Queries a collection by the specifying conditions that form a filter.

This action's `queryArray` parameter specifies an array of arrays (or an empty array for no filtering conditions). Each inner array specifies a condition of a filter. Multiple inner arrays represent multiple conditions joined by an AND operator.

Each element in an inner condition array represents a part of the condition. A condition array always has the following three elements:

  - A left element specifying the collection field.
  - A middle element specifying the operator.
  - A right element specifying the collection field value.

The following example specifies two condition arrays to filter based on the collection's `state` and `population` fields:

```xml
<Input><![CDATA[{
  "colName":"cities",
  "queryArray":[
    ["state", "==", "CA"],
    ["population","<",1000000]
  ]
}]]></Input>
```
At runtime, this action is interpreted as a query such as the following:

> Select all of the cities where state = 'CA' and population < 1000000

A query that includes multiple conditions must be supported in Cloud Firestore by a composite index. For more information, see [Index types in Cloud Firestore](https://firebase.google.com/docs/firestore/query-data/index-overview).

#### Request parameters

  * {Syntax}

  ```xml
  <Input><![CDATA[{
    "colName" : "firestore-collection-name",
    "queryArray" : "queryArray": query-condition-array
  }]]></Input>
  ```

  * {Example}

    In this example, the `queryArray` parameter specifies two conditions on which to filter the `cities` collection specified by the `colName` parameter. <br/><br/>A query that includes multiple conditions must be supported in Cloud Firestore by a composite index. For more information, see [Index types in Cloud Firestore](https://firebase.google.com/docs/firestore/query-data/index-overview).

    ```xml
    <Input><![CDATA[{
      "colName":"cities",
      "queryArray":[["state", "==", "CA"],["population","<",1000000]]
    }]]></Input>
    ```
    At runtime, this action is interpreted as a query such as the following:

    > Select all of the cities where state = 'CA' and population < 1000000

Parameter | Description | Type | Default | Required
--------- | ----------- | ---- | ------- | --------
colName | Name of the collection to query. | String | None. | Yes.
queryArray | An array of condition arrays that, together, specify the parts of a filter. Specify an empty array to omit conditions (not filter results). | Array | None. | Yes.

#### Response

Object containing the content of the document in JSON format.

### setDocument

Copies a document into a Cloud Firestore collection. If the document already exists in the collection, this overwrites it.

#### Request parameters

```xml
<Input><![CDATA[{
  "colName" : "firestore-collection-name",
  "docName" : "firestore-document-name",
  "data" : "data-to-copy"
}]]></Input>
```

Parameter | Description | Type | Default | Required
--------- | ----------- | ---- | ------- | --------
colName | Name of the collection in which to create the document. | String | None. | Yes.
docName | Name of the document to which `data` should be copied. | String | None. | Yes.
data | Data to copy into `docName`. This must be a valid JSON object. Arrays are not supported. | Object | None. | No.

#### Response

None.

## Configuration Reference

Use the following when you're configuring and deploying this extension for use in API proxies. For steps to configure an extension using the Apigee console, see [Adding and configuring an extension](/api-platform/extensions/configuring-an-extension).

Note: The ability to configure and deploy extensions is available only to system administrators.

### Common extension properties

{% dynamic include /includes/___common-extension-properties %}

### Properties for this extension package

Property | Description | Default | Required
-------- | ----------- | ------- | --------
firestoreDB | URL to the Cloud Firestore database that this extension should use whem making requests. This URL usually takes for the form `https://DATABASE_NAME.firebaseio.com`. | None. | Yes.
credentials | Use the When entered in the Apigee Edge console, this is the contents of the key file you generated with [Firebase instructions](https://firebase.google.com/docs/admin/setup#add_firebase_to_your_app). When sent via the management API, it is a base64-encoded value generated from the key file. | None. | Yes.

{% endblock %}
