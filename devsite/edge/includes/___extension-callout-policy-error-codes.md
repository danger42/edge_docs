This section describes the error messages and flow variables that are set when this policy triggers an error. This information is important to know if you are developing fault rules for a proxy. To learn more, see [What you need to know about policy errors](https://docs.apigee.com/api-platform/fundamentals/what-you-need-know-about-policy-errors) and [Handling faults](https://docs.apigee.com/api-platform/fundamentals/fault-handling).

### Runtime errors

These errors can occur when the policy executes.

Error name | HTTP status | Cause
---------- | ----------- | -----
ExecutionFailed | 500 | The extension responds with an error.

### Deployment errors

These errors can occur when you deploy a proxy containing this policy.

Error name | Occurs when
---------- | -----------
InvalidConnectorInstance | The Extension tag is left empty.
ConnectorInstanceDoesNotExists | The Extension name specified in the Extension tag does not exist in the environment the API proxy is being deployed to.
InvalidInput | The Input tag value is not valid JSON.
InvalidAction | The Action tag is left empty
