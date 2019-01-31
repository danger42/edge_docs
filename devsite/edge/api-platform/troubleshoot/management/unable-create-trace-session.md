Project: /_project.yaml
Book: /_book.yaml

# Unable to create trace session

<aside class="note">Was this troubleshooting playbook helpful? Please let us know
  by clicking <a href="" class="google-feedback"
   data-p="1636213" data-b="docsite" data-context="troubleshooting-feedback">
  Send Feedback
</a>.</aside>


## Symptom

User is unable to create trace session in Edge UI.


## Error Message

You will get an error message in Edge UI as shown below:


```
Error creating trace session for API proxy <api proxy name>, revision <revision number>, environment <environment name>.
Failed to create DebugSession <session number> 
```


Here's the screenshot of a sample error message observed in Edge UI:


<img alt="" src="/api-platform/images/IMG-Trace-Error.png">



## Possible Causes

Some of the possible causes for this error are listed below:


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
   <td><a href="#cause-network-connectivity-issue">Network Connectivity Issue</a>
   </td>
   <td>Communication failure between Management Server and Message Processor due to network connectivity issues or firewall rules.
   </td>
   <td rowspan="7" >Edge Private Cloud Users
   </td>
  </tr>
  <tr>
   <td><a href="#cause-environment-not-loaded-on-the-message-processor">Environment not loaded on the Message Processor</a>
   </td>
   <td>Specific environment (in which you are trying to enable the trace) has not been loaded on the Message Processor (s) due to an error.
   </td>
   
  </tr>
  <tr>
   <td><a href="#cause-stale-message-processor-entries-or-message-processors-unreachable">Stale Message Processor Entries</a>
   </td>
   <td>Management Server is having reference to non-existent (stale) Message Processors.
   </td>
    
  </tr>
  <tr>
   <td><a href="#cause-stale-message-processor-entries-or-message-processors-unreachable">Message Processor Unreachable</a>
   </td>
   <td>Message Processor has been stopped or become unreachable.
   </td>

  </tr>
  <tr>
   <td><a href="#cause-high-resource-utilization-issue">High Resource Utilization Issue</a>
   </td>
   <td>Message Processor (s) are experiencing high resource (CPU, Memory or Load) utilization.
   </td>
   
  </tr>
  <tr>
   <td><a href="#cause-api-proxy-not-deployed-on-one-or-more-message-processors">API Proxy not deployed on one or more Message Processors</a>
   </td>
   <td>The API Proxy may not be deployed on one or more Message Processors due to missing event notification during deployment.
   </td>
   
  </tr>
  <tr>
   <td><a href="#cause-issue-with-edge-ui">Issue with Edge UI</a>
   </td>
   <td>Edge UI is unable to create a trace session due to some error.
   </td>
    
  </tr>
</table>



## Common Diagnosis Steps



1.  Execute this management API:

    ```
    curl -v <management-server-host>:8080/v1/runtime/organizations/<org-name>/environments/<env-name>/apis/<apiproxy-name>/revisions/<revision-number>/debugsessions -u <user>
    ```


1.  If you get any errors, note them.  Move to [Network Connectivity Issue](#cause-network-connectivity-issue).
1.  If you get a successful response, then that indicates the trace session can be created via Management API.  However, there could be a possible issue with the Edge UI such that  trace session cannot be created in the UI.  Move to [Issue with Edge UI](#cause-issue-with-edge-ui).


## <a id="cause-network-connectivity-issue">Cause: Network Connectivity Issue


### _Diagnosis_



1.  Check the Management Server log `/opt/apigee/var/log/edge-management-server/logs/system.log` and see if there are any errors during the creation of trace/debug session.

    **Sample error from Management Server Log**


    ```
    2018-02-08 09:08:21,310 org:myorg env:uat  qtp1073741635-1074 ERROR DISTRIBUTION - DebugSessionAPI.createDebugSession() : createDebugSession : Unable to connect to the server with UUID cedeabd2-e4d1-40bb-8f18-d6afc8835e5b
    org.apache.http.conn.HttpHostConnectException: Connect to 10.84.75.92:8082 [/10.84.75.92] failed: Connection refused
    	at org.apache.http.impl.conn.HttpClientConnectionOperator.connect(HttpClientConnectionOperator.java:140) ~[httpclient-4.3.5.jar:4.3.5]
    	at org.apache.http.impl.conn.PoolingHttpClientConnectionManager.connect(PoolingHttpClientConnectionManager.java:318) ~[httpclient-4.3.5.jar:4.3.5]
    	at org.apache.http.impl.execchain.MainClientExec.establishRoute(MainClientExec.java:363) ~[httpclient-4.3.5.jar:4.3.5]
    ...<snipped>
    Caused by: java.net.ConnectException: Connection refused
    	at java.net.PlainSocketImpl.socketConnect(Native Method) ~[na:1.8.0_65]
    	at java.net.AbstractPlainSocketImpl.doConnect(AbstractPlainSocketImpl.java:350) ~[na:1.8.0_65]
    ...<snipped>
    ```


1.  The sample error above shows that we are getting **"Connection refused"** errors when the Management Server tries to connect to the Message Processor on port # 8082.  Hence, the Management Server is unable to create the trace session.
1.  If you don't see any errors related to network connectivity or an error similar to the one shown in the above example, then move to [Environment not loaded on the Message Processor](#cause-environment-not-loaded-on-the-message-processor).
1.  If you observe error(s) related to network connectivity or an error similar to the one as shown in the above example, then follow the below steps.
1.  Test the connectivity from Management Server to the Message Processor on port 8082 using the following steps:
    1.  If telnet is available, then use telnet:


        ```
        telnet <MessageProcessor_IP> 8082
        ```


    1.  If telnet is not available, use netcat to check the connectivity as follows:


        ```
        nc -vz <MessageProcessor_IP> 8082
        ```


    1.  If you get the response "Connection Refused" or "Connection timed out", then move to the next step.
1.  Login to each of the Message Processors with the corresponding IP address that showed the error and perform the following steps:
    1.  Check if the Message Processor is listening on the port 8082:

        ```
        netstat -an | grep LISTEN | grep 8082
        ```


    1.  If the Message Processor is listening on port 8082, then move to step #7.
    1.  If the Message Processor is not listening on port 8082, then restart the Message Processor using this command:

        ```
        /opt/apigee/apigee-service/bin/apigee-service edge-message-processor restart
        ```


    1.  Wait till the Message Processor has completely started using this command:

        ```
        /opt/apigee/apigee-service/bin/apigee-service edge-message-processor wait_for_ready
        ```


    1.  Once the Message Processor is up, re-check if the Message Processor is listening on the port 8082. 
    1.  If the Message Processor is listening on the port 8082, then move to step #7.
1.  Check if you are now able to start the trace session in the UI.  If the problem is no longer observed, skip the below steps.
1.  If the Message Processor is running and is listening on the port 8082, but you are still unable to connect from the other servers like Management Server, then probably there's  a firewall that must be blocking the external connections.
1.  Use appropriate command to check the firewall rules. For example, you could execute the iptables command to list all the firewall rules defined on your system:


  ```
  iptables -L -n
  ```



1.  If there are no firewall rules set for the port 8082, then move to [High Resource Utilization Issue](#cause-high-resource-utilization-issue).
1.  If there are any firewall rules setup on the port 8082, move to Resolution section below.


### _Resolution_



1.  Work with your network administrator to allow the inbound/outbound traffic on the port 8082 from external servers.

If the problem still persists, go to [Must Gather Diagnostic Information](#must-gather-diagnostic-information).


## <a id="cause-environment-not-loaded-on-the-message-processor">Cause: Environment not loaded on the Message Processor


### _Diagnosis_



1.  Check the Management Server logs `/opt/apigee/var/log/edge-management-server/logs/system.log` and see if there are any errors during the creation of trace/debug session.
1.  You may see an error message like "**no valid responses from MP(s)**" during the creation of trace/debug session as shown below:

    ```
    2018-01-30 08:28:09,721 org:mynonprod env:uat  qtp2007599722-712162 ERROR DISTRIBUTION - DebugSessionAPI.createDebugSession() : no valid responses from MP(s), throwing error
    2018-01-30 08:28:09,723 org:mynonprod env:uat  qtp2007599722-712162 ERROR REST - CustomJAXRSInvoker.performInvocation() : CustomJAXRSInvoker.performInvocation : Method com.apigee.distribution.DebugSessionAPI.createDebugSession threw an exception.
    2018-01-30 08:28:09,724 org:mynonprod env:uat  qtp2007599722-712162 ERROR REST - ExceptionMapper.toResponse() : Error occurred : Failed to create DebugSession 1517297564678
    2018-01-30 08:28:09,724 org:mynonprod env:uat  qtp2007599722-712162 ERROR REST - ExceptionMapper.toResponse() : Returning error response : ErrorResponse{errorCode = distribution.CreateDebugSessionFailed, errorMessage = Failed to create DebugSession 1517297564678}
    ```



    This error indicates that the Message Processor(s) are not responding back to Management Server for some reason.

1.  If you don't see an error similar to the one shown in the above example, then move to [Stale Message Processor Entries](#cause-stale-message-processor-entries-or-message-processors-unreachable).
1.  If you do observe an error similar to the one shown in the above example, then follow these steps. 
1.  One of most likely cause for this error is that the environment in which you are trying to create the trace session is not loaded on the Message Processor(s).
1.  Login to each of the Message Processor(s) and check if the specific environment in which you are trying to create the trace session is loaded on the Message Processor using the below command:

    ```
    curl -s http://localhost:8082/v1/runtime/organizations/<org-name>/environments
    ```



    **Example Output:**


    You will see the list of environments belonging to the specific organization that are loaded on the Message Processor in the output of the above command.  For example, if **preprod** and **test** environments are loaded on the Message Processor, then you will see the output as follows:


  **[ "preprod", "test" ]**



1.  If the specific environment, say **"dev",** in which you are trying to create a trace session, is listed as part of the above command, then move to [Stale Message Processor Entries](#cause-stale-message-processor-entries-or-message-processors-unreachable).
1.  If the specific environment, say **"dev",** is not listed as part of the above command, then check the `/opt/apigee/var/log/edge-message-processor/logs/system.log` and `/opt/apigee/var/log/edge-message-processor/logs/startupruntimeerrors.log` on the Message Processors for any errors during loading of Environments.
1.  There could be many different errors that could lead to failure of loading an environment on the Message Processor. Resolution depends on the error occurred.


### Resolution

The Environment may not be loaded on Message Processor due to many reasons.  This section illustrates a couple of possible reasons that can lead to this issue and explains how to resolve the issue. 



1.  If you see one of the following errors in the Message Processor log, then it is caused due to an issue found with the certificates/keys that have been added to the specified keystore/truststore in the specified environment.

    **Error #1: java.security.KeyStoreException: Cannot overwrite own certificate**


    ```
    2018-01-30 12:04:38,248 pool-47-thread-4 ERROR MESSAGING.RUNTIME - AbstractConfigurator.propagateEvent() : Error while handling the update for the Configurator 
    com.apigee.kernel.exceptions.spi.UncheckedException: Failed to add certificate : mycert in key store : mytruststore in environment : test
    at com.apigee.entities.configurators.KeyStore.setCertificateEntry(KeyStore.java:156) ~[config-entities-1.0.0.jar:na] 
    at com.apigee.entities.configurators.KeyStore.handleUpdate(KeyStore.java:101) ~[config-entities-1.0.0.jar:na] 
    at com.apigee.entities.AbstractConfigurator.propagateEvent(AbstractConfigurator.java:85) ~[config-entities-1.0.0.jar:na] 
    at com.apigee.messaging.runtime.Environment.handleUpdate(Environment.java:238) [message-processor-1.0.0.jar:na] 
    … 
    Caused by: java.security.KeyStoreException: Cannot overwrite own certificate 
    at com.sun.crypto.provider.JceKeyStore.engineSetCertificateEntry(JceKeyStore.java:355) ~[sunjce_provider.jar:1.8.0_151] 
    at java.security.KeyStore.setCertificateEntry(KeyStore.java:1201) ~[na:1.8.0_151] 
    at com.apigee.entities.configurators.KeyStore.setCertificateEntry(KeyStore.java:153) ~[config-entities-1.0.0.jar:na]
    ... 20 common frames omitted
    2018-01-30 12:04:38,250 pool-47-thread-4 ERROR MESSAGING.RUNTIME - AbstractConfigurator.rollbackTransaction() : Error in processing the changes : Unknown resource type cert
    ```



    **Error #2: java.security.KeyStoreException: Cannot overwrite secret key**


    ```
    2017-11-01 03:28:47,560 pool-21-thread-7 ERROR MESSAGING.RUNTIME - AbstractConfigurator.propagateEvent() : Error while handling the update for the Configurator 
    com.apigee.kernel.exceptions.spi.UncheckedException: Failed to add certificate : mstore in key store : myTruststore in environment : dev 
    at com.apigee.entities.configurators.KeyStore.setCertificateEntry(KeyStore.java:156) ~[config-entities-1.0.0.jar:na] 
    at com.apigee.entities.configurators.KeyStore.handleUpdate(KeyStore.java:101) ~[config-entities-1.0.0.jar:na] 
    ... 
    Caused by: java.security.KeyStoreException: Cannot overwrite secret key 
    at com.sun.crypto.provider.JceKeyStore.engineSetCertificateEntry(JceKeyStore.java:354) ~[sunjce_provider.jar:1.8.0_144] 
    at java.security.KeyStore.setCertificateEntry(KeyStore.java:1201) ~[na:1.8.0_144] 
    at com.apigee.entities.configurators.KeyStore.setCertificateEntry(KeyStore.java:153) ~[config-entities-1.0.0.jar:na] 
    ... 20 common frames omitted 

    2017-11-01 03:28:47,562 pool-21-thread-7 ERROR MESSAGING.RUNTIME - AbstractConfigurator.rollbackTransaction() : Error in processing the changes : Unknown resource type cert 
    ```


1.  Get the details of the keystore/truststore specified in the error message shown in the previous step by using the following  management API call:

    ```
    curl -v "http://<management-IPaddress>:8080/v1/organizations/<org-name>/environments/<env-name>/keystores/myTruststore" -u <user>
    ```

  <strong>Example output:</strong></code>

    ```
    { 
    "certs": [ 
    "mycert", 
    "mycert-new" 
    ], 
    "keys": [ 
    "mycert" 
    ], 
    "name": "myTruststore" 
    }
    ```


1.  The example output shows that there are two certificates and a key in the truststore **myTruststore**.  The truststore generally does not contain a key.  If it does, it is better to have a single certificate and a single key.
1.  Get the details about the two certificates using the following API:

    ```
    curl -s http://<management-IPaddress>:8080/v1/runtime/organizations/<org-name>/environments/<env-name>/keystores/<keystore-name>/certs/<cert-name>
    ```


1.  Check the expiry date of each of the certificates and determine the expired/older certificate.
1.  Delete the expired or unwanted certificate from the truststore "myTruststore".

If the problem still persists or if you see any error other than the ones mentioned in Step #1 above, go to [Must Gather Diagnostic Information](#must-gather-diagnostic-information).


## <a id="cause-stale-message-processor-entries-or-message-processors-unreachable">Cause: Stale Message Processor Entries OR Message Processors Unreachable


### Diagnosis



1.  If the Edge UI takes a long time and fails to create the trace session, then here are some of the possible causes:
    1.  The Management Server may be referring to non existent (stale) Message Processors
    1.  The Message Processor(s) has been stopped or become unreachable
    1.  The Message Processors are running into high memory/CPU usage
1.  Check the Management Server logs `/opt/apigee/var/log/edge-management-server/logs/system.log` and see if there are any errors during creation of trace/debug session.
1.  You may see an error message like **"server \<UUID\> is either not up or reachable"** during the creation of trace/debug session as shown below:

    ```
    2017-12-27 07:42:38,975 org:cocacola env:prod qtp2007599722-222063 INFO DISTRIBUTION - DebugSessionAPI.createDebugSession() : server 458b5910-2646-441c-a6e2-428b6d84e021 is either not up or reachable, skipping the server
    ```



    This may be followed one more error "Connection timed out" after a little while as shown below:


    ```
    2017-12-27 07:44:46.000 UTC org:cocacola env:prod qtp2007599722-222063 ERROR DISTRIBUTION - DebugSessionAPI.createDebugSession() : createDebugSession : Unable to connect to the server with UUID {}, skipping it458b5910-2646-441c-a6e2-428b6d84e021 org.apache.http.conn.HttpHostConnectException: Connect to 192.168.101.7:8080 [/192.168.101.7] failed: Connection timed out (Connection timed out) at org.apache.http.impl.conn.HttpClientConnectionOperator.connect(HttpClientConnectionOperator.java:140) ~[httpclient-4.3.5.jar:4.3.5] at org.apache.http.impl.conn.PoolingHttpClientConnectionManager.connect(PoolingHttpClientConnectionManager.java:318) ~[httpclient-4.3.5.jar:4.3.5] at org.apache.http.impl.execchain.MainClientExec.establishRoute(MainClientExec.java:363) ~[httpclient-4.3.5.jar:4.3.5] at org.apache.http.impl.execchain.MainClientExec.execute(MainClientExec.java:219) ~[httpclient-4.3.5.jar:4.3.5] 
    …<snipped>
    Caused by: java.net.ConnectException: Connection timed out (Connection timed out) at java.net.PlainSocketImpl.socketConnect(Native Method) ~[na:1.8.0_144] at java.net.AbstractPlainSocketImpl.doConnect(AbstractPlainSocketImpl.java:350) ~[na:1.8.0_144]
    …<snipped>
    ```


1.  These two errors could be caused either due to specific Message Processor(s) :
    1.  Being stale (not existing anymore)
    1.  Being down/not reachable for some reason
1.  Please follow the appropriate resolution depending on the scenario encountered.


### Resolution


### Scenario #1 : Message Processor(s) are stale (non existent)



1.  Get the list of Message Processors using the below management API:

    ```
    curl -u <sysadmin> "http://<management-server-host>:8080/v1/servers?pod=<podName>&regions=<regionName>"
    ```


1.  Note down the IP address or hostname that correspond to the UUID (s) of the Message Processors mentioned in the error message in Management Server logs (step #3 in Diagnosis above).  Verify if these are valid Message Processors using one of the following ways:
    1.  Latest Private Cloud topology setup diagram
    1.  Latest Edge Server IP address - Host Name mapping table 

    If you find them to be valid Message Processors, then move to [Scenario 2 : Message Processor (s) are unreachable](#scenario_2_message_processors_are_unreachable).

1.  Delete the stale (non existent) Message Processors using the below management APIs:
    1.  Deregister Message Processor from the organization's environments: 

        ```
        curl -X POST http://<management-server-host>:8080/v1/o/<orgName>/e/<envName>/servers -d "uuid={uuid}&region=<regionName>&pod=<podName}&action=remove" 
        ```


    1.  Deregister server's type: 

        ```
        curl http://<management-server-host>:8080/v1/servers -X POST -d "type={message-processor}&region=<regionName>&pod=<podName>&uuid=<uuid>&action=remove"
        ```


    1.  Delete the server: 

        ```
        curl http://<management-ip>:8080/v1/servers/<uuid> -X DELETE
        ```


1.  Repeat the Step #3 if you have the same issue in any other environments in your organization.


### Scenario 2: Message Processor(s) are unreachable



1.  Login to each of the Message Processor(s) by determining the IP addresses/Host names based on the UUIDs observed in the error message in Management Server logs.
1.  Restart the Message Processor:

    ```
    /opt/apigee/apigee-service/bin/apigee-service edge-message-processor restart

    ```


Re-check if you are able to create the trace session.  If the problem persists, go to [Must Gather Diagnostic Information](#must-gather-diagnostic-information)**.**


## <a id="cause-high-resource-utilization-issue">Cause: High Resource Utilization Issue

### Diagnosis



1.  Login to each of the Message Processor(s) and check if there is high utilization of any resources - CPU, Memory or Load. You can use the `top` command on Unix based operating systems to get the resource utilization information of Message Processor process:

    ```
    top
    ```


1.  If the Message Processor(s) are not experiencing high resource utilization, then move to [Must Gather Diagnostic Information](#must-gather-diagnostic-information).
1.  If the Message Processor(s) is experiencing high CPU or memory usage, then that might be causing the Message Processor not to respond back to Management Server in time.  This eventually prevents you from being able to create trace session.
    1.  If any Message Processor is experiencing high CPU usage, then generate three [thread dumps](https://docs.apigee.com/api-platform/troubleshoot/diagnostic-tools-and-logs#threaddumps) every 30 seconds using the following command:

        ```
        sudo <JAVA_HOME>/bin/jstack -l <pid> > <filename>
        ```


    1.  If any Message Processor is having high Memory usage, then generate a [heap dump](https://docs.apigee.com/api-platform/troubleshoot/diagnostic-tools-and-logs#heapdumps) using the following command:

        ```
        sudo -u apigee <JAVA_HOME>/bin/jmap -dump:live,format=b,file=<filename> <pid>
        

        ```


    1.  Move to Resolution.


### Resolution



1.  Restart the Message Processor using the below command. This should bring down the CPU and memory usage:

    ```
    /opt/apigee/apigee-service/bin/apigee-service edge-message-processor restart
    ```


1.  Monitor the API calls and confirm if the problem still exists.



1.  Contact [Apigee Support](http://support.apigee.com/) and provide the thread dumps, heap dump, and Message Processor logs (`/opt/apigee/var/log/edge-message-processor/logs/system.log)` to help them investigate the cause for the high CPU/memory usage.


## <a id="cause-api-proxy-not-deployed-on-one-or-more-message-processors">Cause: API Proxy not deployed on one or more Message Processors

Rarely an API Proxy may not be deployed on one or more Message Processors. This happens mostly due to missing event notification from the Management Server to the Message Processor during the deployment of the specific API Proxy.  In this case also, you will not be able to create the trace session in the Edge UI.


### Diagnosis



1.  Login to each of the Message Processors and check if the specific revision of the API Proxy is deployed using the following command:

    ```
    curl -v localhost:8082/v1/runtime/organizations/<orgname>/environments/<envname>/apis/<apiname>/revisions
    ```



    **Example Output:**


    You will see the list of revisions as the output of the above command. For example, if revision 12 is deployed then you will see the output as follows:


    **[ "12" ]**

1.  If the specific revision of the API Proxy does not show up as the output of the command mentioned in Step #1 above, then restart the specific Message Processor as explained in the **Resolution** below.
1.  Repeat the steps 1-2 for all the Message Processors.
1.  If the specific revision of the API Proxy is deployed on all the Message Processors, then this is not the cause for this issue. Move to [Must Gather Diagnostic Information](#must-gather-diagnostic-information).


### Resolution



1.  Restart the specific Message Processor(s) on which the specific revision of the API Proxy is not deployed:

    ```
    /opt/apigee/apigee-service/bin/apigee-service edge-message-processor restart

    ```



## <a id="cause-issue-with-edge-ui">Cause: Issue with Edge UI


### Diagnosis



1.  Check the Edge UI logs `/opt/apigee/var/log/edge-ui/application.log` and `/opt/apigee/var/log/edge-ui/edge-ui.log` and see if there are any errors.
1.  Contact [Apigee Support](https://support.apigee.com/) and share these files for further investigation.


## <a id="must-gather-diagnostic-information">Must Gather Diagnostic Information

If the problem persists even after following the above instructions, please gather the following diagnostic information. Contact and share them to [Apigee Support](https://support.apigee.com/):



1.  Output of the command:

    ```
    curl -v <management-server-host>:8080/v1/runtime/organizations/<org-name>/environments/<env-name>/apis/<apiproxy-name>/revisions/<revision-number>/debugsessions -u <user>
    ```


1.  Management Server log 

    ```
    /opt/apigee/var/log/edge-management-server/logs/system.log.
    ```


1.  Message Processor logs

    ```
    /opt/apigee/var/log/edge-message-processor/logs/system.log.
    ```


1.  Output of telnet/nc commands from Management Server to Message Processor:

    ```
    telnet <MessageProcessor_IP> 8082
    nc -vz <MessageProcessor_IP> 8082
    ```


1.  Output of the below netstat command on the Message Processor(s):

    ```
    netstat -an > netstat.txt
    ```


1.  If it was found to be an issue with Edge UI, then provide the Edge UI logs `/opt/apigee/var/log/edge-ui/application.log` and `/opt/apigee/var/log/edge-ui/edge-ui.log.`
1.  Details about what sections in this Playbook has been tried and any other insights that will help us to fastrack resolution of this issue.
