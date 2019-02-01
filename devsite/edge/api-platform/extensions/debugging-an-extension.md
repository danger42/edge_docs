{% extends "_base.html" %}
{% block title %}Debugging an extension{% endblock %}
{% block body %}

{{ extensions_availability }}

You can debug an extension by using messages visible in two places: the Trace tool and extension logs. When an extension isn't working, identifying the problem can sometimes require information from both places.

- The Apigee Edge [Trace tool](/api-platform/debug/using-trace-tool-0) is where you iteratively test and edit API proxy code as you develop it. Trace messages include errors from your API proxy code, including API proxy and policy configuration.

  Extension-related errors that appear in the Trace tool don't usually contain much detail, except to say which extension callout failed, along with an HTTP error code. When you don't see anything useful here, the next best place to look is the log for the extension you're using.

- Extensions generate [log entries](/api-platform/extensions/viewing-extension-logs) at runtime. (Extension logs are available for organization administrators only.)

  These logs include entries returned by the external resource the extension is configured to interact with. For example, if external resource credentials are misconfigured in the extension, the error is likely to appear here.

  The logs also include entries from internal extension code. When you're looking through the logs, keep in mind that some of the entries aren't relevant to the error you're correcting. Extension-related log entries usually begin with the word `details`, as in the following log entry from the [Cloud Pub/Sub](/api-platform/reference/extensions/google-cloud-pub-sub-extension) extension:

  ```
  details: 'Invalid resource name given (name=projects/example-test-123456/topic/extension-example). Refer to https://cloud.google.com/pubsub/docs/admin#resource_names for more information.'
  ```

## Error types and causes

Extension request processing flows from an Extension Callout policy in an API proxy, through the extension, out to the external resource, then back again. So an error can occur in any of those places.

Errors you see might fall into the following categories.

### Errors in extension configuration

This is the [configuration that an organization administrator does](/api-platform/extensions/configuring-an-extension) when adding an extension to an environment.

For example, if you configure the Stackdriver Logging extension with an incorrect GCP project ID, Google Stackdriver will return an error to the extension. Details about these errors are usually in the extension log.

#### Evidence in the Trace tool

In the proxy editor, these errors will typically show up as a 4xx- or 5xx-level error. However, the proxy editor won't display any specifics about the error's cause except to say that the extension returned an error.

```json
{
  "fault": {
    "faultstring":"Execution of ConnectorCallout Logging-Extension failed. Reason: Connector returned error statuscode=500",
    "detail": {
      "errorcode":"steps.connectorcallout.ExecutionFailed"
    }
  }
}
```

#### Evidence in extension logs

If there's detail about this kind of error, you'll see it in the extension's log entries. The following error message, returned by the [Cloud Pub/Sub service](https://cloud.google.com/pubsub/docs/overview), results from a malformed project ID.

```
details: 'Project does not exist: example-test-12345'
```

### Errors in Extension Callout policy configuration

These errors occur when the [Extension Callout policy](/api-platform/reference/policies/extension-callout-policy) is misconfigured, either through a policy configuration syntax error or through incorrect configuration keys or values. These errors take two forms, depending on how the policy is configured:

- Incorrect values evaluated by the external resource

  This can occur when the configuration error appeared valid to the extension, but was invalid for the external resource. For example, if the extension passes an incorrect database ID to [Cloud Spanner](https://cloud.google.com/spanner/docs/overview), Cloud Spanner will return an error that is logged in the extension log:

  ```
  details: 'Database not found: projects/example-test-123456/instances/spanner-extension-example-db/databases/my-business-d'
  ```
  This can occur also for incorrect configuration JSON in the policy's `<Input>` element. For some extensions, a portion of the JSON is processed by the extension and a portion is passed to the resource. For example, the [Stackdriver Logging extension](/api-platform/reference/extensions/google-stackdriver-logging-extension) configuration JSON includes a `metadata` object whose contents are passed to [Stackdriver Logging](https://cloud.google.com/logging/docs/basic-concepts). Incorrect key names there -- such as `typ` instead of `type` -- can return errors from the external resource that appear as entries in the extension log:

  ```
  details: 'Resource type cannot be empty'
  ```

- Incorrect values evaluated by the extension

  These errors include syntax errors in policy-evaluated parts of `<Input>` element JSON, misspelling the action name in the `<Action>` element, and so on. These errors will usually appear in the Trace tool, but not in the extension logs.

#### Evidence in the Trace tool

In the proxy editor, these errors will typically show up as a 4xx- or 5xx-level error. However, the proxy editor won't display any specifics about the error's cause except to say that the extension returned an error. The following error appears in the Trace tool when misspelling the action name in the [Cloud Firestore extension](/api-platform/reference/extensions/google-cloud-firestore-extension).

```json
{
  "fault":{
    "faultstring":"Execution of ConnectorCallout Add-User-Data failed. Reason: Connector returned error statuscode=404","detail":
    {
      "errorcode":"steps.connectorcallout.ExecutionFailed"
    }
  }
}
```

#### Evidence in extension logs

When the policy configuration results in a processing error in the external resource, the error will usually appear in the log.

### Errors related to the extension's request to the resource

This is an error in which the request to the external resource didn't succeed for reasons that aren't related to the extension.

For example, imagine you're using the [Cloud Spanner extension](/api-platform/reference/extensions/google-cloud-spanner-database-extension) to add a row to the database, but the row's primary key value is already used in an existing row. Cloud Spanner will return an error to the extension, which will add the error to the extension log.

#### Evidence in the Trace tool

In the proxy editor, these errors will typically show up as a 4xx- or 5xx-level error. However, the proxy editor won't display any specifics about the error's cause except to say that the extension returned an error.

```json
{
  "fault":{
    "faultstring":"Execution of ConnectorCallout Add-User-Data failed. Reason: Connector returned error statuscode=404",
    "detail":{
      "errorcode":"steps.connectorcallout.ExecutionFailed"
    }
  }
}
```

#### Evidence in extension logs

The log will usually have entries with messages from the external resource itself. The following log message from Cloud Spanner describes the existing primary key value error.

```
details: 'Row [jonesy42] in table user already exists'
```

{% endblock %}
