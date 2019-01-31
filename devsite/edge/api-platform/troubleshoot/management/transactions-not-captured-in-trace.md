{% extends "_base.html" %} {% block title %}API requests not captured in the Trace UI{% endblock %} {% block body %}

<aside class="note">Was this troubleshooting playbook helpful? Please let us know
  by clicking <a href="" class="google-feedback"
   data-p="1636213" data-b="docsite" data-context="troubleshooting-feedback">
  Send Feedback</a>.</aside>


## Symptom

<img class="screenshot" width="80%" alt="" src="/api-platform/images/transactions-not-captured.png">


API requests are not captured in the Edge UI Trace when a trace session is started.


## Error Message

No error messages will be displayed in the Edge UI when this problem occurs.


## Possible Causes


<table>
  <tr>
   <td width="25%"><strong>Cause</strong>
   </td>
   <td width="50%"><strong>Description</strong>
   </td>
   <td width="25%"><strong>Troubleshooting Instructions Applicable For</strong>
   </td>
  </tr>
  <tr>
   <td><a href="#cause-requests-not-processed">Requests not processed by the Message Processor</a>
   </td>
   <td>API requests must be processed by Edge's component Message Processor in order to capture trace. If an API request fails to reach Apigee Edge, fails at the entry point to Edge (i.e., Router) or fails before it is processed by the Message Processor, then the trace cannot be captured.
   </td>
   <td>Edge Public and Private Cloud Users
   </td>
  </tr>
  <tr>
   <td><a href="#not-found-classification-tree">API Proxy Not Found in The Classification Tree</a>
   </td>
   <td>Apigee Message Processors use a Routing Rule definition called Classification Tree for dispatching requests based on the hostname, base path, revision, and environment of the incoming request. If the relevant API proxy is removed for some reason from the Classification Tree, the Trace transactions may not populate.
   </td>
   <td>Edge Private Cloud Users
   </td>
  </tr>
</table>



## <a id="cause-requests-not-processed">Cause: Requests not processed by the Message Processor


### _Diagnosis_

In order to capture an API request in a Trace session, the API request must be processed by Edge's component Message Processor.  There are several reasons why an API request may not be captured in a Trace transaction.

For example, if an API request fails to reach Apigee Edge, fails at the entry point to Edge (i.e., Router) or fails before it gets processed by the Message Processor, then the trace cannot be captured. Each of these scenarios is outlined in greater detail below.

#### Scenario 1:  Requests fail to reach Apigee Edge

*   Cause

    In this scenario, the error may be caused by a DNS resolution or network connectivity issue. If so, you may see the following error when running this command:

    <pre class="devsite-terminal">
    curl https://<var>hostName</var>:<var>port</var>/<var>apiProxyBasePath</var>/<var>requestPath</var>
    </pre>
    <pre>
    curl: (6) Could not resolve host: <var>hostName</var>
    </pre>

*   Resolution

    You can verify DNS configuration with the following command:

  <pre class="devsite-terminal">dig <var>hostName</var></pre>

    You can verify network connectivity with the following command:

  <pre class="devsite-terminal">telnet <var>hostName</var> <var>port</var></pre>


#### Scenario 2:  Requests fail at the Apigee Edge Router

*   Cause

    In this scenario, the error may be caused by a TLS/SSL handshake failure. If so, you may see one of the following errors:

    ```
    Received fatal alert: handshake_failure
    ```
    ```
    HTTP/1.1 400 Bad Request
    ```

    You may also see an SSL certificate error.

*   Resolution

    Refer to the following playbooks to troubleshoot and resolve these issues:


    [TLS/SSL Handshake Failures](https://docs.apigee.com/api-platform/troubleshoot/runtime/ssl-handshake-failures)


    [400 Bad Request - SSL Certificate Error](https://docs.apigee.com/api-platform/troubleshoot/runtime/400-ssl-certificate-error)



#### Scenario 3:  Requests cannot be processed by Message Processor

*   Cause

    In this scenario, the Apigee Message Processor cannot find the API Proxy for
    the specified virtual host and path.  As a result, you may see one of the
    following errors:

    ```
    HTTP/1.1 404 Not Found
    ```

    <pre class="prettyprint">
    {
      "fault":{
        "faultstring":"Unable to identify proxy for host: default and url: \/<var>apiProxyBasePath</var>/<var>requestPath</var>",
        "detail":{
          "errorcode":"messaging.adaptors.http.flow.ApplicationNotFound"
        }
      }
    }

    </pre>

*   Resolution

    Refer to this playbook to troubleshoot and resolve this issue:   [404 Unable to identify proxy for host](https://docs.apigee.com/api-platform/troubleshoot/runtime/404-unable-identify-proxy-host).



Note: If you are not observing any of these issues, then proceed to <a href="#not-found-classification-tree">Cause: API Proxy Not Found in The Classification Tree</a>.


## <a id="not-found-classification-tree">Cause: API Proxy Not Found in The Classification Tree


### _Diagnosis_

If an API proxy is not found in the Classification Tree of one or more Message Processors, then any API requests to that specific proxy will not be shown in Trace sessions in the Edge UI.

Follow the steps below to determine if this is the case:



1.  Log in to each of the Message Processors and check to see if the specific revision of the requested API is deployed in the relevant environment of the Message Processor using the following command:

    <pre class="devsite-terminal">
    curl -v http://localhost:8082/v1/runtime/organizations/<var>orgName</var>/environments/<var>envName</var>/apis/<var>apiName</var>/revisions
    </pre>



    **Example Output:**


    The command above will output a list of deployed revisions. For example, if revision 12 is deployed then you will see the following output:


    ```
    [ "12" ]
    ```



    Unless you are encountering intermittent HTTP 404 errors, you are likely to see that the specific revision is deployed.

1.  Read the Classification Tree and check for the existence of the API proxy name using the following command:

    <pre class="devsite-terminal">
    curl -i http://localhost:8082/v1/classification/tree | grep <var>apiName</var>
    </pre>


1.  Repeat Steps 1 and 2 for each Message Processor. If the given API proxy name is not found in the Classification Tree on any of the Message Processors, then follow the resolution given below.


### _Resolution_

Please follow the steps below to resolve this issue. Make sure to take any precautions necessary to avoid production outages that may occur from restarting Message Processors while experiencing high request loads.



1.  Log in to each of the Message Processor hosts that are missing the specific API proxy in the Classification Tree and use the command below to restart the Message Processor:

    <pre class="devsite-terminal">
    /opt/apigee/apigee-service/bin/apigee-service edge-message-processor restart
    </pre>


1.  Once restarted, use the command below to wait until it becomes active:

    <pre class="devsite-terminal">
    /opt/apigee/apigee-service/bin/apigee-service edge-message-processor wait_for_ready
    </pre>


1.  Once the Message Processor is ready, verify the availability of the API proxy using the following command:

    <pre class="devsite-terminal">
    curl -v http://localhost:8082/v1/runtime/organizations/<var>orgName</var>/environments/<var>envName</var>/apis/<var>apiName</var>/revisions
    </pre>



    **Example Output:**


    The command above will output a list of deployed revisions. For example, if revision 12 is deployed then you will see the following output:


    ```
    [ "12" ]
    ```



    Unless you are encountering intermittent HTTP 404 errors, you are likely to see that the specific revision is deployed.

1.  Read the Classification Tree and verify the existence of the API proxy name using the following command:

    <pre class="devsite-terminal">
    curl -i http://localhost:8082/v1/classification/tree | grep <var>apiName</var>
    </pre>



  If the problem still persists, go to <a href="#must-gather-diagnostic">Must Gather Diagnostic Information</a>.


## <a id="must-gather-diagnostic">Must Gather Diagnostic Information

If the problem persists after following the instructions above, please gather the following diagnostic information and share with [Apigee Support](https://support.apigee.com/):



1.  Output of the command:

    <pre class="devsite-terminal">
curl -v <var>management-server-host</var>:8080/v1/runtime/organizations/<var>orgName</var>/environments/<var>envName</var>/apis/<var>apiProxyName</var>/revisions/<var>revisionNumber</var>/debugsessions -u <var>user</var>
</pre>


1.  Management Server log:

    <pre class="devsite-terminal">
/opt/apigee/var/log/edge-management-server/logs/system.log
</pre>


1.  Message Processor logs:

    <pre class="devsite-terminal">
/opt/apigee/var/log/edge-message-processor/logs/system.log
</pre>


1.  Output of telnet/nc commands from Management Server to the Message Processor:

    <pre class="prettyprint">
    <code class="devsite-terminal">telnet <MessageProcessor_IP> 8082</code>
    <code class="devsite-terminal">nc -vz <MessageProcessor_IP> 8082</code>
    </pre>


1.  Output of the below netstat command on the Message Processor(s):

    <pre class="devsite-terminal">
netstat -an > netstat.txt
</pre>


1.  Output of the following command listing the revisions deployed for the specific API Proxy on all the Message Processor(s):

    <pre class="devsite-terminal">
curl -v http://localhost:8082/v1/runtime/organizations/<var>orgName</var>/environments/<var>envName</var>/apis/<var>apiName</var>/revisions
</pre>


1.  Classification tree output on all the Message Processor(s):


    <pre class="devsite-terminal">
    curl -i http://localhost:8082/v1/classification/tree
    </pre>



{% endblock %}
