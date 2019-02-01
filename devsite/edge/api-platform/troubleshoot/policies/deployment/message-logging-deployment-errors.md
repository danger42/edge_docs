{% extends "_base.html" %} {% block title %}Message Logging policy deployment error troubleshooting{% endblock %} {% block body %}


## InvalidProtocol

### Error message

Deployment of an API proxy through either the Edge UI or Edge management API fails with this error message:

<pre>
Error Saving Revision <var>revision_number</var>
Invalid Protocol: <var>invalid_protocol</var> for Syslog handler in policy <var>policy_name</var>.
</pre>

### Example error message

```
Error Saving Revision 4
Invalid Protocol: HTTP for Syslog handler in policy LogToSyslog.
```

### Example screenshot

<img alt="" src="/api-platform/images/messagelogging-error-2.png" width="75%">

### **Cause**

The deployment of the {{message_logging_policy}} policy can fail with this error if the protocol specified within the `<Protocol>` element is not valid. The valid protocols are TCP and UDP.  For sending syslog messages over TLS/SSL, only TCP is supported.

For example, the deployment of the API Proxy fails with this error  if you specify HTTP in the `<Protocol>` element of the MessageLogging Policy as shown below:

```
<Protocol>HTTP</Protocol>
```

### **Diagnosis**


1.  Identify the MessageLogging policy where the error occurred. You can find this information in the error message. For example, in the following error, the policy name is `LogToSyslog:`

    ```
    Invalid Protocol: HTTP for Syslog handler in policy LogToSyslog.
    ```

1.  In the failed MessageLogging policy configuration check if the protocol specified within the `<Protocol>` element is valid. If there is an invalid protocol specified, then that's the cause of the error.

    For example, the following policy specifies an invalid protocol.

    ```
    <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
    <MessageLogging name="LogToSyslog">
      <Syslog>
        <Message>[3f509b58 tag="{organization.name}.{apiproxy.name}.{environment.name}"] Weather request for WOEID {request.queryparam.w}.</Message>
        <Host>logs-01.loggly.com</Host>
        <Port>514</Port>
        <Protocol>HTTP</Protocol>
        <FormatMessage>true</FormatMessage>
      </Syslog>
      <logLevel>ALERT</logLevel>
    </MessageLogging>
    ```

    Because the protocol defined within the `<Protocol>` element is invalid, the deployment of the API Proxy fails with the error:

    ```
    Invalid Protocol: HTTP for Syslog handler in policy LogToSyslog.
    ```

### Resolution

Ensure that the protocol defined within the `<Protocol>` element of the MessageLogging policy is valid. The `<Protocol>`
element supports the following values:

  * TCP
  * UDP

To correct the example shown above, define a valid protocol within the `<Protocol>` element:

```
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<MessageLogging name="LogToSyslog">
  <Syslog>
    <Message>[3f509b58 tag="{organization.name}.{apiproxy.name}.{environment.name}"] Weather request for WOEID {request.queryparam.w}.</Message>
    <Host>logs-01.loggly.com</Host>
    <Port>514</Port>
    <Protocol>TCP</Protocol>
    <FormatMessage>true</FormatMessage>
  </Syslog>
  <logLevel>ALERT</logLevel>
</MessageLogging>
```


## InvalidPort


### Error message

Deployment of an API proxy through either the Edge UI or Edge management API fails with this error message:

<pre>
Error Saving Revision <var>revision_number</var>
Invalid Port: 0 for Syslog handler in policy <var>policy_name</var>.
</pre>

### Example error message

```
Error Saving Revision 4
Invalid Port: 0 for Syslog handler in policy LogToSyslog.
```

### Example screenshot

<img alt="" src="/api-platform/images/messagelogging-error-1.png" width="75%">

### Cause

The deployment of the {{message_logging_policy}} can fail with this error if the port number is not specified within the `<Port>` element or if it is not valid. The port number must be an integer greater than zero.

For example, if the `<Port>` element is declared as shown below, the deployment of the API proxy fails:

```
<Port>0</Port>
```

### Diagnosis


1.  Identify the MessageLogging policy where the error occurred. You can find this information from the error message. For example, in the following error, the policy name is `LogToSyslog:`

    ```
    Invalid Port: 0 for Syslog handler in policy LogToSyslog.
    ```

1.  In the failed MessageLogging policy check if a valid port number is specified within the `<Port>` element. If the port number is not defined or is invalid, then that's the cause of the error.

    For example, the following policy specifies an invalid port number:

    ```
    <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
    <MessageLogging name="LogToSyslog">
      <Syslog>
        <Message>[3f509b58 tag="{organization.name}.{apiproxy.name}.{environment.name}"] Weather request for WOEID {request.queryparam.w}.</Message>
        <Host>logs-01.loggly.com</Host>
        <Port>0</Port>
        <Protocol>TCP</Protocol>
        <FormatMessage>true</FormatMessage>
      </Syslog>
      <logLevel>ALERT</logLevel>
    </MessageLogging>
    ```

    Because the port number defined within the `<Port>` element is invalid, the deployment of the API Proxy fails with the error:

    ```
    Invalid Port: 0 for Syslog handler in policy LogToSyslog.
    ```

### Resolution

Ensure that a port number is defined within the `<Port>` element of the MessageLogging policy. It has to be an integer greater than zero. 

To correct the example shown above, define a valid port number within the `<Protocol>` element. 


```
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<MessageLogging name="LogToSyslog">
  <Syslog>
    <Message>[3f509b58 tag="{organization.name}.{apiproxy.name}.{environment.name}"] Weather request for WOEID {request.queryparam.w}.</Message>
    <Host>logs-01.loggly.com</Host>
    <Port>514</Port>
    <Protocol>TCP</Protocol>
    <FormatMessage>true</FormatMessage>
  </Syslog>
  <logLevel>ALERT</logLevel>
</MessageLogging>
```

{% endblock %}
