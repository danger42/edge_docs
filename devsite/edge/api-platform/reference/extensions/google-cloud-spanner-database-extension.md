{% extends "_base.html" %}
{% block title %}Google Cloud Spanner Database Extension{% endblock %}
{% block body %}

{{ extensions_availability }}

Perform insert, query, and update operations on a Cloud Spanner database.

This content provides reference for configuring and using this extension.
Before using the extension from an API proxy using the
{{ extension_callout_policy }}, you must:

1. Create a Cloud Spanner instance, as described in [Creating and Managing Instances](https://cloud.google.com/spanner/docs/create-manage-instances), and create a database.

1. Once you have the instance and a database, [grant permission](https://cloud.google.com/iam/docs/granting-roles-to-service-accounts) for accessing your database to the GCP service account that represents your extension. For more on the role to use, see [Cloud Spanner roles](https://cloud.google.com/iam/docs/understanding-roles#cloud_spanner_roles). For more on Cloud Spanner access control, see [Applying IAM Roles](https://cloud.google.com/spanner/docs/grant-permissions) and [Access Control for Cloud Spanner](https://cloud.google.com/spanner/docs/iam).

1. When you have a service account that has permission for the level of access to your database you want, [generate a key for the service account](https://cloud.google.com/iam/docs/creating-managing-service-account-keys). Use the contents of the resulting key JSON file when you configure this extension.

1. Use the contents of the resulting key JSON file when [adding and configuring](/api-platform/extensions/configuring-an-extension) the extension using the [configuration reference](#configuration_reference).

## About Cloud Spanner

[Cloud Spanner](https://cloud.google.com/spanner/docs/overview) is a relational database service that is useful for relational, structured, and semi-structured data that requires high availability, strong consistency, and transactional reads and writes.

If you're just getting started with Cloud Spanner, the [quickstart in the Cloud Spanner documentation](https://cloud.google.com/spanner/docs/quickstart-console) is a good place to begin.

## Samples

The following examples illustrate how to configure support for Cloud Spanner extension actions using the [ExtensionCallout policy](/api-platform/reference/policies/extension-callout-policy).

  * {Add data}

    In the following example, the extension's `insert` action adds a new user to the user table.

    ```xml
    <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
    <ConnectorCallout async="false" continueOnError="true" enabled="true" name="Insert-New-User">
        <DisplayName>Insert New User</DisplayName>
        <Connector>spanner-users-products</Connector>
        <Action>insert</Action>
        <Input><![CDATA[{
            "tableName" : "user",
            "rows" : [{
              "username": "jonesy42",
              "firstName": "Floyd",
              "lastName": "Jones",
              "address": "3695 Auctor Street",
              "city": "Gresham",
              "region": "OR",
              "postalCode": "12693",
              "email": "floydster@example.com"
          }]
      }]]></Input>
    </ConnectorCallout>
    ```

  * {Get data}

    In this example, a query retrieves username and email values from the `user` table.

    First, an {{ assign_message_policy }} assigns a `postal.code.value` variable for use in a query WHERE clause. This is an example. Your policy would probably set the value based on client request parameters.

    ```xml
    <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
    <AssignMessage async="false" continueOnError="false" enabled="true" name="Assign-Postal-Code">
        <AssignTo createNew="true" transport="http" type="request"/>
        <AssignVariable>
            <Name>postal.code</Name>
            <Value>86519</Value>
        </AssignVariable>
        <IgnoreUnresolvedVariables>true</IgnoreUnresolvedVariables>
    </AssignMessage>
    ```

    The following {{ extension_callout_policy }} executes a query against the database, using the contents of the `postal.code.value` variable in the WHERE clause.

    ```xml
    <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
    <ConnectorCallout async="false" continueOnError="true" enabled="true" name="Get-User-Data">
        <DisplayName>Get User Data</DisplayName>
        <Connector>spanner-users-products</Connector>
        <Action>querySQL</Action>
        <Input><![CDATA[{
          "sql" : "SELECT username, email FROM user WHERE postalCode = @postalCode",
          "params" : {
            "postalCode" : "{postal.code.value}"
          }
        }]]></Input>
      <Output>spanner.userdata.retrieved</Output>
    </ConnectorCallout>
    ```

    The following {{ assign_message_policy }} then uses the extension's response,
    stored in the `spanner.userdata.retrieved` variable, as the response returned
    to the client.

    ```xml
    <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
    <AssignMessage async="false" continueOnError="false" enabled="true" name="Get-Query-Response-Data">
        <DisplayName>Get Query Response Data</DisplayName>
        <AssignTo type="response" createNew="false"/>
        <Set>
            <Payload contentType="application/json">{spanner.userdata.retrieved}</Payload>
        </Set>
    </AssignMessage>
    ```

    In this example, the response data is returned as JSON such as the following.

    ```json
    {
      "rows": [
        {
          "username": "freewill444",
          "email": "freewill@example.com"
        }
      ]
    }
    ```

  * {Update data}

    In this example, the `<Input>` element contains the `username` -- the table's primary key -- and a new value for the `email` column.

    ```xml
    <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
    <ConnectorCallout async="false" continueOnError="true" enabled="true" name="Update-User-Data">
        <DisplayName>Update User Data</DisplayName>
        <Connector>spanner-users-products</Connector>
        <Action>update</Action>
        <Input><![CDATA[{
            "tableName" : "user",
            "rows": [{
                "username":"Liz456",
                "email":"lizzard@example.com"
            }]
        }]]></Input>
    </ConnectorCallout>
    ```

## Actions

### insert

Inserts the specified rows into the database.

* {Syntax}

  ```xml
  <Action>insert</Action>
  <Input><![CDATA[{
    "tableName" : "table-to-insert-into",
    "rows" : "rows-to-insert"
  }]]></Input>
  ```
* {Example}

  In the following example, the extension's `insert` action adds a new user to the user table. One row is added.

  ```xml
  <Action>insert</Action>
  <Input><![CDATA[{
      "tableName" : "user",
      "rows" : [{
        "username": "jonesy42",
        "firstName": "Floyd",
        "lastName": "Jones",
        "address": "3695 Auctor Street",
        "city": "Gresham",
        "region": "OR",
        "postalCode": "12693",
        "email": "floydster@example.com"
    }]
  }]]></Input>
  ```

#### Request parameters

Parameter | Description | Type | Default | Required
--------- | ----------- | ---- | ------- | --------
tableName | The table in the database to which the rows should be inserted. | String | None. | Yes.
rows | The rows to insert expressed as an array in a `rows` JSON object. | Array | None. | Yes.

#### Response

None.

### querySQL

Queries the database using the SQL statement with the specified parameters. Parameters are given in the SQL statement with @-prepended names; parameter values are specified in this action's `params` parameter.

For details about Cloud Spanner query syntax, see [Query Syntax](https://cloud.google.com/spanner/docs/query-syntax).

* {Syntax}

  ```xml
  <Action>querySQL</Action>
  <Input><![CDATA[{
    "sql" : "sql-query-statement",
    "params" : {
      "param1" : "columnValue"
    }
  }]]></Input>
  ```

* {Example}

  In this example, a query retrieves `username` and `email` column values from the `user` table. The SQL statement specifies a `postalCode` parameter that's set from the flow variable `postal.code.value`.

  ```xml
  <Action>querySQL</Action>
  <Input><![CDATA[{
    "sql" : "SELECT username, email FROM user WHERE postalCode = @postalCode",
    "params" : {
      "postalCode" : "{postal.code.value}"
    }
  }]]></Input>
  ```

#### Request parameters

Parameter | Description | Type | Default | Required
--------- | ----------- | ---- | ------- | --------
sql | The SQL query to execute. You can specify parameters with @-prepended parameter names. Those parameter names must correspond to keys in the `params` parameter of this action. | String | None. | Yes.
params | An object whose keys and values are the parameter names and values used in the SQL query. You can list multiple parameters here. | Object | None. | No.

#### Response

A `rows` object containing an array of column name-value pairs returned by the query. For example:

```json
{
  "rows": [
    {
      "username": "freewill444",
      "email": "freewill@example.com"
    }
  ]
}
```

### update

Updates rows in the database with the specified data.

* {Syntax}

  ```xml
  <Input><![CDATA[{
    "tableName" : "table-with-rows-to-update",
    "rows" : "rows-to-update"
  }]]></Input>
  ```

* {Example}

  In this example, the email address for the user whose `username` is Liz456 is updated with a new value. One row is updated.

  ```xml
  <Action>update</Action>
  <Input><![CDATA[{
    "tableName" : "user",
    "rows": [{
        "username":"Liz456",
        "email":"lizzard@example.com"
    }]
  }]]></Input>
  ```

#### Request parameters

Parameter | Description | Type | Default | Required
--------- | ----------- | ---- | ------- | --------
tableName | The table in the database where rows should be updated. | String | None. | Yes.
rows | An array of row data to update. Each entity in the array should contain the unique ID (such as primary key) value for the row to update. | Array | None. | Yes.

#### Response

None.

## Configuration Reference

Use the following when you're configuring and deploying this extension for use in API proxies. For steps to configure an extension using the Apigee console, see [Adding and configuring an extension](/api-platform/extensions/configuring-an-extension).

Note: The ability to configure and deploy extensions is available only to system administrators.

### Common extension properties

{% dynamic include /includes/___common-extension-properties %}

### Properties for this extension package

Specify values for the following configuration properties specific to this extension.

Property | Description | Default | Required
-------- | ----------- | ------- | --------
projectId | ID of the GCP project containing the database. | None. | Yes.
instanceId | ID of the Cloud Spanner instance in your GCP project. | None. | Yes.
databaseId | ID of the Cloud Spanner database. | None. | Yes.
credentials | When entered in the Apigee Edge console, this is the contents of your [service account key file](https://cloud.google.com/iam/docs/creating-managing-service-account-keys). When sent via the management API, it is a base64-encoded value generated from the service account key file. | None. | Yes.

{% endblock %}
