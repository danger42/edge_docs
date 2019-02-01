Project: /_project.yaml
Book: /_book.yaml

# Deployment Error: Configuration failed

<aside class="note">Was this troubleshooting playbook helpful? Please let us know
  by clicking <a href="" class="google-feedback"
   data-p="1636213" data-b="docsite" data-context="troubleshooting-feedback">
  Send Feedback
</a>.</aside>


## Symptom

Deployment of API proxy or Shared Flow revisions through the Edge UI or management API fails with a **Configuration failed** error.


## Error Message

You will get an error message in the Edge UI as shown below:


```
The revision is deployed, but traffic cannot flow.
com.apigee.kernel.exceptions.spi.UncheckedException{ code = application.bootstrap.FailedToConfigure, message = Configuration failed, associated contexts = []}
```


Here's the screenshot of a sample error message observed in Edge UI:


<img alt="" src="/api-platform/images/IMG-deployment-error-config-failed.png">



## Possible Causes

The deployment of an API Proxy can fail with "Configuration failed" error due to many different reasons. The below table lists a few frequently observed causes that lead to this error :


<table>
  <tr>
   <td><strong>Cause</strong>
   </td>
   <td><strong>Description</strong>
   </td>
   <td><strong>Troubleshooting Instructions Applicable For</strong>
   </td>
  </tr>
  <tr>
   <td><a href="#missing-java-class-in-javacallout-policy">Missing Java Class in JavaCallout Policy</a>
   </td>
   <td>A Java class is missing from the JAR file being referenced by the JavaCallout policy.
   </td>
   <td rowspan="4" >Edge Private Cloud Users
   </td>
  </tr>
  <tr>
   <td><a href="#cause-incorrect-operands-used-with-operators-in-the-condition-flow">Incorrect operands used in conditions in the Condition Flow</a>
   </td>
   <td>Operands/expressions used on one or both sides of the operators in the conditions are not valid.
   </td>
  </tr>
  <tr>
   <td><a href="#invalid-host-name-in-messagelogging-policy">Invalid Hostname in Message Logging policy</a>
   </td>
   <td>The hostname used in the MessageLogging policy cannot be resolved or may have some unwanted special characters.
   </td>
  </tr>
  <tr>
   <td><a href="#cause-invalid-keyvaluemap-name">Invalid KeyValueMap Name</a>
   </td>
   <td>The KeyValueMap is invalid or empty in the KeyValueMapOperations policy in the API Proxy.
   </td>
  </tr>
</table>



## Common Diagnosis Steps



1.  Get the deployments status for the specific revision of the API Proxy for which you are observing the deployment error using the below API:

    ```
    curl -v <management-server-host>:<port#>/v1/runtime/organizations/<org-name>/environments/<env-name>/apis/<apiproxy-name>/revisions/deployments -u <user>
    ```


1.  Here's an example output from the above API;

    ```
    "server" : [ { 
    "error" : "com.apigee.kernel.exceptions.spi.UncheckedException{ code = application.bootstrap.FailedToConfigure, message = Configuration failed, associated contexts = []}", 
    "status" : "error", 
    "type" : [ "message-processor" ], 
    "uUID" : "0a20926c-f4bf-401b-af84-05fd84b9f492" 
    }, { 
    "error" : "com.apigee.kernel.exceptions.spi.UncheckedException{ code = application.bootstrap.FailedToConfigure, message = Configuration failed, associated contexts = []}", 
    "status" : "error", 
    "type" : [ "message-processor" ], 
    "uUID" : "f2ee6ab4-a108-4465-a7ba-b56530d8e3fc" 
    }, { 
    "error" : "com.apigee.kernel.exceptions.spi.UncheckedException{ code = application.bootstrap.FailedToConfigure, message = Configuration failed, associated contexts = []}", 
    "status" : "error", 
    "type" : [ "message-processor" ], 
    "uUID" : "0f41991e-b310-4e77-aac5-5fdb150ef9f6" 
    },
    ```


1.  You will see the error message **"Configuration failed"** in each of the Message Processors in the deployment status output.
1.  Log in into one of the Message Processor(s) and check the log `/opt/apigee/var/log/edge-message-processor/logs/system.log`. See if there are any errors during the deployment of the API Proxy.
1.  Depending on the error/exception observed in the Message Processor log, you need to follow the appropriate troubleshooting steps and resolution for the issue.
1.  The below sections provide some of the most frequently observed exceptions that lead to the deployment error "**Configuration failed"** and provide steps to troubleshoot and resolve them.

<aside class="note">These troubleshooting instructions can be carried out only by Private Cloud users.</aside>


## <a id=missing-java-class-in-javacallout-policy>Cause: Missing Java Class in JavaCallout Policy


### Diagnosis



1.  In the Message Processor logs, if you see any exception with the message **"Failed to instantiate the JavaCallout Class"** during the deployment of an API Proxy (DeployEvent) as shown below, then proceed to Step #2. If not, go to [Incorrect operands used in conditions in the Condition Flow](#cause-incorrect-operands-used-with-operators-in-the-condition-flow).  
1.  The Message Processor shows the following exception during the deployment of the API Proxy:

    ```
    2017-10-10 05:02:42,330 Apigee-Main-5 ERROR MESSAGING.CONFIGURATION - MessageProcessorServiceImpl.configure() : error configuring config events [DeployEvent{organization='myorg', application='oauth2', applicationRevision='14', deploymentSpec=basepath=/;env=dev;, deploymentID=null}] 
    com.apigee.kernel.exceptions.spi.UncheckedException: Failed to instantiate the JavaCallout Class com.something.apigee.callout.crypto.main.SecretCallout 
    at com.apigee.steps.javacallout.JavaCalloutStepDefinition.newInstance(JavaCalloutStepDefinition.java:89) ~[javacallout-1.0.0.jar:na] 
    at com.apigee.messaging.runtime.StepDefinition.getStepDefinitionExecution(StepDefinition.java:230) ~[message-processor-1.0.0.jar:na] 
    …
    <snipped>
    ```


1.  The error message in the above exception indicates that the JavaCallout class `com.something.apigee.callout.crypto.main.SecretCallout` could not be instantiated. This error generally occurs when the specific class is not available in the JAR file specified in the JavaCallout policy or any of its dependent JAR files.
1.  Check the JAR file that contained all the classes pertaining to the package `com.something.apigee.callout.crypto.main` and confirm that the specific class `com.something.apigee.callout.crypto.main.SecretCallout` was missing.


### Resolution



1.  Add the missing class to the specific JAR file and upload the JAR file.
1.  Redeploy the API Proxy.
1.  In the above example, we resolved the problem by:
    1.  Adding the missing class `com.something.apigee.callout.crypto.main.SecretCallout` to the JAR file.
    1.  Uploading the updated JAR file and redeploying the API Proxy. 


## <a id="cause-incorrect-operands-used-with-operators-in-the-condition-flow">Cause: Incorrect operands used with operators in the Condition Flow


### Diagnosis



1.  In the Message Processor logs, if you see a <code>com.apigee.expressions.parser.ParseException<strong> </strong></code>during the deployment of an API Proxy or Shared Flow as shown in the example messages below, then proceed to Step #2. If not, go to next cause [Invalid Hostname in Message Logging policy](#invalid-host-name-in-messagelogging-policy).

    <strong>Example Error Message</strong>


    ```
    com.apigee.expressions.parser.ParseException: Both the operands for EQUALS expression should be data expressions

    ```



1.  Let's look at an example to understand how to diagnose this issue.

  **Example : Operands for \<Operator\> expression should be data expressions**

1.  The Message Processor shows the following exception during the deployment of a Shared Flow:

    ```
    2017-11-23 09:11:04,498  Apigee-Main-6 ERROR MESSAGING.RUNTIME - AbstractConfigurator.loadXMLConfigurations() : Unable to Load default for path /organizations/myorg/apiproxies/Introspection/revisions/12/sharedflows/default
2017-11-23 09:11:04,499  Apigee-Main-6 ERROR MESSAGING.RUNTIME - Application.sync() :  sync error for Introspection and revision 12
2017-11-23 09:11:04,499  Apigee-Main-6 ERROR MESSAGING.RUNTIME - Application.sync() :  Actual Error
com.apigee.expressions.parser.ParseException: Both the operands for EQUALS expression should be data expressions
        at com.apigee.expressions.parser.ExpressionParser.buildExpressionTree(ExpressionParser.java:337) ~[expressions-1.0.0.jar:na]
        at com.apigee.expressions.parser.ExpressionParser.parse(ExpressionParser.java:24) ~[expressions-1.0.0.jar:na]
        at com.apigee.expressions.parser.ExpressionParser.parseLogicExpression(ExpressionParser.java:28) ~[expressions-1.0.0.jar:na]
        at com.apigee.messaging.runtime.Step.getExpression(Step.java:67) ~[message-processor-1.0.0.jar:na]
        at com.apigee.messaging.runtime.Step.handleAdd(Step.java:58) ~[message-processor-1.0.0.jar:na]
        at com.apigee.messaging.runtime.SharedFlowRuntime.addStep(SharedFlowRuntime.java:81) ~[message-processor-1.0.0.jar:na] … <snipped>
    ```


1.  The error message in the ParseException - "`Both the operands for EQUALS expression should be data expressions`" indicates that a condition involving equal to (=), not equal to (!=)  or Stats with (=|) operator has an issue.

    <aside class="note">Though this particular error message indicates an issue with **EQUALS** operator, the same error message can be observed with any other operator - **or**, **and**, etc</aside>

1.  Look at the conditions in all the Condition Flows involving the specific operator mentioned in the error message and see if there are any of the following issues:
    1.  The expressions on either side of the operator are of same type.  For example, if you have a string variable on the left side of the operator, then you need to have another string variable or string value on the right side.
    1.  Valid variables are used between the operators.
    1.  There's a space between the operator and each of the expressions.

    <aside class="note">If the issue occurs in an API Proxy, then you need to check the Condition Flows in both the Proxy and Target Endpoints.

1.  If any of the above mentioned criteria is not met, then you get the ParseException - "`Both the operands for EQUALS expression should be data expressions`".
1.  Let's look at an example to understand this issue.  Here's a sample error condition tha

    ```
    <Condition>
               (fault.name = "invalid_access_token") or(fault.name = "ApiKeyNotApproved")
    </Condition>
    ```


1.  In this example, you can observe that there's no space between the "or" operator and then next condition.  So when the second condition is being parsed, the first expression is taken as **"or(fault.name"** for the EQUALS operator.  This is not a valid variable name, so it is not treated as a valid data expression.  As a consequence you get this exception:

    ```
    com.apigee.expressions.parser.ParseException: Both the operands for EQUALS expression should be data expressions

    ```



### Resolution



1.  Ensure that you always have proper data expressions on either side of the operators.
1.  In the example discussed above, the resolution was to ensure that there was space after the "or" operator as described in the code snippet:

    ```
    <Condition>
               (fault.name = "invalid_access_token") or (fault.name = "ApiKeyNotApproved")
    </Condition>

    ```



## <a id="invalid-host-name-in-messagelogging-policy">Invalid Host Name in MessageLogging Policy


### Diagnosis



1.  In the Message Processor logs, if you see any exception with the message **"Invalid HostName"** during the deployment of API Proxy or Shared Flow as shown below, then proceed to Step #2. If not, go to next cause [Invalid KeyValueMap Name](#cause-invalid-keyvaluemap-name).  

    ```
    com.apigee.rest.framework.ValidationException: Invalid syslog config: Invalid HostName 'splunkprod.myorg.com/' for Syslog handler
    ```


1.  Let's look at below two examples to understand how to troubleshoot and resolve this issue.

**Example 1: HostName having unwanted special character**



  1.  The Message Processor shows the following exception during the deployment of the API Proxy:

      ```
      2018-01-20 02:12:13,535 Apigee-Main-3 ERROR MESSAGING.CONFIGURATION - MessageProcessorServiceImpl.configure() : error configuring config events [DeployEvent{organization='myorg', application='providersearch', applicationRevision='4', deploymentSpec=basepath=/;env=prod;, deploymentID=null}] 
      com.apigee.rest.framework.ValidationException: Invalid syslog config: Invalid HostName 'splunkprod.myorg.com/' for Syslog handler 
      at com.apigee.messaging.runtime.destinations.SyslogDestination.<init>(SyslogDestination.java:44) ~[message-processor-1.0.0.jar:na] 
      at com.apigee.messaging.runtime.destinations.SysLoggerFactory.getInstance(SysLoggerFactory.java:39) ~[message-processor-1.0.0.jar:na]
      at com.apigee.messaging.runtime.destinations.DestinationRegistry.newDestination(DestinationRegistry.java:44) ~[message-processor-1.0.0.jar:na] 
      ...<snipped>
      ```


  1.  The exception above shows that deployment is failing due to **"Invalid HostName '\<hostname\>' for Syslog handler"**.  This indicates that the HostName used in the **MessageLogging** Policy is an invalid hostname.
  1.  Examining the exception in the Message Processor log carefully shows that there's an unwanted special character "/" at the end of the HostName `'splunkprod.myorg.com/'.`
  1.  This unwanted special character was the cause for the deployment error.


### Resolution



1.  Modify the MessageLogging policy to remove any unwanted special characters to resolve the issue.
1.  In the above example, the special character "/" was removed from the MessageLogging Policy.  This resolved the issue.

**Example 2: Unresolvable Host Name**



1.  The Message Processor log had a few lines that shows the deployment event for an API Proxy is triggered, which is followed by an exception that occurs during the deployment of the API Proxy:

    ```
    2017-12-22 00:13:49,057 Apigee-Main-87446 INFO MESSAGING.CONFIGURATION - MessageProcessorServiceImpl.configure() : configuring [DeployEvent{organization='myorg', application='myapi', applicationRevision='42', deploymentSpec=basepath=/;env=dev;, deploymentID=null}] 

    2017-12-22 00:13:49,318 Apigee-Main-87446 ERROR c.a.p.h.d.DNSCachedAddress - DNSCachedAddress.refresh() : Unable to resolve host : input-prd.cloud.splunk.com: Name or service not known 

    2017-12-22 00:13:49,323 Apigee-Main-87446 ERROR MESSAGING.RUNTIME - AbstractConfigurator.handleUpdate() : Fatal error deploying proxy: {} 
    com.apigee.rest.framework.ValidationException: Invalid syslog config: Invalid HostName 'input-prd.cloud.splunk.com' for Syslog handler 
    at com.apigee.messaging.runtime.destinations.SyslogDestination.<init>(SyslogDestination.java:44) ~[message-processor-1.0.0.jar:na] 
    at com.apigee.messaging.runtime.destinations.SysLoggerFactory.getInstance(SysLoggerFactory.java:39) ~[message-processor-1.0.0.jar:na] 
    at com.apigee.messaging.runtime.destinations.DestinationRegistry.newDestination(DestinationRegistry.java:44) ~[message-processor-1.0.0.jar:na] 
    at com.apigee.steps.messagelogging.MessageLoggingStepDefinition.populateDestinations(MessageLoggingStepDefinition.java:118) ~[message-logging-1.0.0.jar:na] 
    at com.apigee.steps.messagelogging.MessageLoggingStepDefinition.handleAdd(MessageLoggingStepDefinition.java:99) ~[message-logging-1.0.0.jar:na] 
    …
    <snipped> 
    ```


1.  The exception above shows that deployment is failing due to **"Invalid HostName '\<hostname\>' for Syslog handler"**.
1.  If you read through the line above the exception, you can notice that the Message Processor is unable to resolve the hostname `'input-prd.cloud.splunk.com'` provided in the MessageLogging policy.
1.  To confirm this you can try to telnet to the hostname and port # used in the Message Logging policy.
    1.  Check the MessageLogging policy in the specific revision of the API Proxy and verify the hostname and port # used.  In the example above, the API Proxy name: myapi, revision: 42.

      **MessageLogging Policy**

          ```
          <MessageLogging async="false" continueOnError="false" enabled="true" name="Log-To-Splunk">
              <DisplayName>Log-To-Splunk</DisplayName>
              <Syslog>
                  <Message>Message.id = {request.header.id}</Message>
                  <Host>input-prd.cloud.splunk.com</Host>
                  <Port>2900</Port>
                  <Protocol>TCP</Protocol>
                  <SSLInfo>
                      <Enabled>true</Enabled>
                  </SSLInfo>
              </Syslog>
          </MessageLogging>
          ```

    1.  Telnet to the host with specific port.  For this example, we tried the telnet and got the same error as seen in the Message Processor log:

        ```
        telnet input-prd.cloud.splunk.com 2900 
        telnet: input-prd.cloud.splunk.com: Name or service not known 
        input-prd.cloud.splunk.com: Host name lookup failure
        ```


1.  This clearly proved that the hostname cannot be resolved. 


### Resolution



1.  Modify the MessageLogging policy to use the valid host name.

If the problem still persists, go to [Must Gather Diagnostic Information](#must-gather-diagnostic-information).


## <a id="cause-invalid-keyvaluemap-name">Cause: Invalid KeyValueMap Name


### Diagnosis



1.  In the Message Processor logs, if you see an exception with the message **"KeyValueMap name  is invalid"** during the deployment of an API Proxy or Shared Flow as shown below, then proceed to Step #2. If not, go to [Must Gather Diagnostic Information](#must-gather-diagnostic-information).

    ```
    com.apigee.rest.framework.ValidationException: Invalid syslog config: Invalid HostName 'splunkprod.myorg.com/' for Syslog handler
    ```


1.  Let's look at an example to understand how to troubleshoot and resolve this issue.
1.  **Example Message Processor log** showing the exception with message "KeyValueMap name is invalid" leading to error during API Proxy deployment

    ```
    2018-02-27 14:14:50,318  Apigee-Main-6 ERROR MESSAGING.RUNTIME - AbstractConfigurator.handleUpdate() : Fatal error deploying proxy: {}
    com.apigee.keyvaluemap.KeyValueMapApiException: KeyValueMap name  is invalid
            at com.apigee.keyvaluemap.service.legacy.KeyValueMapServiceImpl.validateMapName(KeyValueMapServiceImpl.java:125) ~[keyvaluemap-1.0.0.jar:na]
            at com.apigee.keyvaluemap.service.legacy.KeyValueMapServiceImpl.createOrUpdateKeyValueMap(KeyValueMapServiceImpl.java:185) ~[keyvaluemap-1.0.0.jar:na]
            at com.apigee.steps.keyvaluemapoperations.KeyValueMapOperationsStepDefinition.digest(KeyValueMapOperationsStepDefinition.java:180) ~[keyvaluemap-operations-1.0.0.jar:na]
            at com.apigee.steps.keyvaluemapoperations.KeyValueMapOperationsStepDefinition.handleAdd(KeyValueMapOperationsStepDefinition.java:197) ~[keyvaluemap-operations-1.0.0.jar:na]
            at com.apigee.entities.AbstractConfigurator.handleUpdate(AbstractConfigurator.java:130) [config-entities-1.0.0.jar:na]
            at com.apigee.messaging.runtime.Application.handleUpdate(Application.java:229) [message-processor-1.0.0.jar:na]

    2018-02-27 14:14:50,344  Apigee-Main-6 ERROR BOOTSTRAP - RuntimeConfigurationServiceImpl.dispatchToListeners() : RuntimeConfigurationServiceImpl.dispatchToListeners : Error occurred while dispatching the request DeployEvent{organization='myorg', application='CustomerAPI', applicationRevision='1', deploymentSpec=basepath=/;env=test;, deploymentID=null} to com.apigee.application.bootstrap.listeners.MessageProcessorBootstrapListener@5009d06e
    com.apigee.keyvaluemap.KeyValueMapApiException: KeyValueMap name  is invalid
            at com.apigee.keyvaluemap.service.legacy.KeyValueMapServiceImpl.validateMapName(KeyValueMapServiceImpl.java:125) ~[keyvaluemap-1.0.0.jar:na]
            at com.apigee.keyvaluemap.service.legacy.KeyValueMapServiceImpl.createOrUpdateKeyValueMap(KeyValueMapServiceImpl.java:185) ~[keyvaluemap-1.0.0.jar:na]
            at com.apigee.steps.keyvaluemapoperations.KeyValueMapOperationsStepDefinition.digest(KeyValueMapOperationsStepDefinition.java:180) ~[keyvaluemap-operations-1.0.0.jar:na]
            at com.apigee.steps.keyvaluemapoperations.KeyValueMapOperationsStepDefinition.handleAdd(KeyValueMapOperationsStepDefinition.java:197) ~[keyvaluemap-operations-1.0.0.jar:na]
            at com.apigee.entities.AbstractConfigurator.handleUpdate(AbstractConfigurator.java:130) ~[config-entities-1.0.0.jar:na]
            at com.apigee.messaging.runtime.Application.handleUpdate(Application.java:229) ~[message-processor-1.0.0.jar:na]
    ```


1.  The second exception above indicates that the deployment error occurred for **API Proxy: CustomerAPI, revision: 1.**
1.  Checking the stacktrace, you can notice that error is thrown while executing the KeyValuMapOperations policy.
1.  Looking into the API Proxy bundle you find that there is one KeyValuMapOperations policy which has the code as shown below:

   
   ```
  <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
  <KeyValueMapOperations async="false" continueOnError="false" enabled="true" name="Pulling-Keys" mapIdentifier="">
    <DisplayName>Pulling Keys</DisplayName>
    <Properties/>
    <ExclusiveCache>false</ExclusiveCache>

   ```

1.  As seen above, the **mapIdentifier**, which indicates the name of the KeyValueMap, has an empty string.  The KeyValueMap name cannot be empty string.  This was the cause for the Deployment error.


### Resolution



1.  Modify the KeyValueMapOperations policy to have a proper valid name for the KeyValueMap.
1.  In the above example, we resolved the issue by modifying the KeyValueMapOperations to have KeyValueMap name as "MyKeyValueMap" as shown below:

      ```
      <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
      <KeyValueMapOperations async="false" continueOnError="false" enabled="true" name="Pulling-Keys" mapIdentifier="MyKeyValueMap">
        <DisplayName>Pulling Keys</DisplayName>
        <Properties/>
        <ExclusiveCache>false</ExclusiveCache>
      ```




## <a id="must-gather-diagnostic-information"> Must Gather Diagnostic Information

If the problem persists even after following the above instructions, please gather the following diagnostic information. Contact [Apigee Support](https://support.apigee.com/) and provide them with the gathered information.



1.  Output of the command

    ```
    curl -v <management-server-host>:<port #>/v1/runtime/organizations/<org-name>/environments/<env-name>/apis/<apiproxy-name>/revisions/deployments -u <user>
    ```


1.  Message Processor logs

    ```
    /opt/apigee/var/log/edge-message-processor/logs/system.log
    ```


1.  Details about what sections in this Playbook has been tried and any other insights that will help us to fastrack resolution of this issue.
