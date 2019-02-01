{% extends "_base.html" %} {% block title %}502 Bad Gateway Timeout Error{% endblock %} {% block body %}

<aside class="note">Was this troubleshooting playbook helpful? Please let us know
  by clicking <a href="" class="google-feedback"
   data-p="1636213" data-b="docsite" data-context="troubleshooting-feedback">
  Send Feedback 
</a>.</aside>



## Symptom

The client application receives a **502 Bad Gateway** error. The Message Processor
returns this error to the client application when it does not receive a response
from a backend server.


## Error message

The client application receives the following response code:


```
HTTP/1.1 502 Bad Gateway
```

In addition, you may observe the following error message:

```
{
 "fault": {
    "faultstring":"Bad Gateway",
    "detail":{
        "errorcode":"messaging.adaptors.http.flow.BadGateway"
    }
 }
}
```

## Possible Cause

The possible cause for this issue is listed in the following table:

<table>
  <tr>
   <td><strong>Cause</strong>
   </td>
   <td><strong>Description</strong>
   </td>
   <td><strong>Troubleshooting steps can be performed by</strong>
   </td>
  </tr>
  <tr>
   <td><a href="#cause_tlsssl_handshake_timeout">TLS/SSL handshake timeout</a>
   </td>
   <td>A timeout occurs during the TLS/SSL Handshake between the Message Processor and backend server.
   </td>
   <td>Edge Private and Public Cloud users
   </td>
  </tr>
</table>


## Cause: TLS/SSL handshake timeout 

In Apigee Edge, you can set up a [TLS/SSL connection to the backend server](/api-platform/system-administration/configuring-ssl-edge-backend-service) to enable TLS communication
between the Edge Message Processor and a backend server.

A TLS/SSL handshake involves multiple steps. This error typically happens when
the TLS/SSL handshake between the Message Processor and a backend server times out.


## Diagnosis 

This section explains how to correctly diagnose a TLS/SSL handshake timeout.
Instructions for Edge Private Cloud and Public Cloud are listed.


### Investigate Trace session output

The following steps explain how to do a preliminary diagnosis of the problem
using the Apigee Edge Trace tool. 



1.  In the Edge UI, enable a [Trace session](/api-platform/debug/using-trace-tool-0) for the affected API proxy.
1.  If the trace for the failing API request shows the following, it is likely that a TLS/SSL handshake timeout error occurred. The probable cause of the error is that the backend server firewall is blocking traffic from Apigee. 
    1.  Determine if the **502 Bad Gateway** error occurs after **55 seconds**, which
    is the default timeout period set on the Message Processor. If you see that
    the error occurred after 55 seconds, this tells you that a timeout was the
    likely cause of the problem. 
    1.  Determine if the error shows the fault: **messaging.adaptors.http.BadGateway**.
    Again, this error usually indicates a timeout occurred. 
    1.  If you are on **Edge Private Cloud**, note the value of the
    **X-Apigee.Message-ID** field in the trace output as shown below. A Private
    Cloud user can use this ID value to do further troubleshooting, as explained later. 
        1.  Click the **Analytics Data Recorded** icon in the trace path:

          <img alt="" class="screenshot" src="/api-platform/images/502-bad-gateway-timout-2.png">

        1.  Scroll down and note the value of the field called **X-Apigee.Message-ID**.

To confirm that TLS/SSL Handshake timeout was the cause of the error, follow the steps in the following sections depending on whether you are on Public Cloud or Private Cloud.


### Additional diagnostic steps for Edge Private Cloud users only

If you are on Apigee Edge Private Cloud, you can perform the following steps to help verify the cause of the handshake error. In this step, you inspect the Message Processor log file for relevant information. If you are on Edge Public Cloud you can skip this section and go to [Further Diagnostic Steps for Private and Public Cloud Users](#further_diagnostic_steps_for_edge_private_and_public_cloud_users). 



1.  Check if you can connect to the specific backend server directly from each of the Message Processors using the `telnet` command:
    1.  If the backend server resolves to a single IP address, then use this command:

      <pre class="devsite-terminal">
      telnet <var>BackendServer-IPaddress</var> 443</pre>

    1.  If the backend server resolves to multiple IP addresses, then use the hostname of the backend server in the telnet command as shown below:

      <pre class="devsite-terminal">
      telnet <var>BackendServer-HostName</var> 443</pre>

    If you are able to connect to the backend server without any errors, go on to the next step. 

    If the `telnet` command fails, you need to work with your network team to check the connectivity between the message processor and the backend server.

1.  Check the Message Processor log file for evidence of a handshake failure. Open the file:
 
  `/opt/apigee/var/log/edge-message-processor/system.log` 
 
  and search for the unique message ID (the value of **X-Apigee.Message-ID** that you
  found in the trace file). Determine if you see a handshake error message associated
  with the message ID as shown below:

    ```
    org:xxx env:xxx api:xxx rev:x messageid:<MESSAGE_ID> NIOThread@1 ERROR HTTP.CLIENT -
    HTTPClient$Context.handshakeTimeout() : SSLClientChannel[Connected: Remote:X.X.X.X:443
    Local:X.X.X.X]@739028 useCount=1 bytesRead=0 bytesWritten=0 age=55221ms lastIO=55221ms
    isOpen=true handshake timeout	
    ```


If you see this error in the message processor's log file, continue further investigation. Go to [Further diagnostic steps for Edge Private and Public Cloud users](#further_diagnostic_steps_for_edge_private_and_public_cloud_users). 

If you do not see the handshake message in the log file, go to [Must Gather Diagnostic Information](#must_gather_diagnostic_information)

### Further diagnostic steps for Edge Private and Public Cloud users

To further pinpoint the problem, you can use the [tcpdump](/api-platform/troubleshoot/diagnostic-tools-and-logs#tcpippacketsniffertcpdumputility) tool to analyze TCP/IP packets to confirm if a timeout occurred during the TLS/SSL handshake. 

1.  If you are a **Private Cloud user**, you can capture the TCP/IP packets on the
backend server or Message Processor.  Preferably, capture them on the backend
server, because the packets are decrypted on the backend server.
1.  If you are a **Public Cloud user**, you do not have access to the Message
Processor; however, capturing the TCP/IP packets on the backend server may help
pinpoint a problem. 
1.  After deciding where to capture the TCP/IP packets, use the following
[tcpdump](/api-platform/troubleshoot/diagnostic-tools-and-logs#tcpippacketsniffertcpdumputility)
command to capture TCP/IP packets.

  ```
  tcpdump -i any -s 0 host <IP address> -w <File name>
  ```

  * If you are taking the TCP/IP packets on the backend server, then use the public
  IP address of the Message Processor in the `tcpdump` command. For help using the
  command to examine backend server traffic, see [tcpdump](/api-platform/troubleshoot/diagnostic-tools-and-logs#tcpippacketsniffertcpdumputility).

  * If you are taking the TCP/IP packets on the Message Processor, then use the public
  IP address of the backend server in the `tcpdump` command. For help using the command
  to examine Message Processor traffic, see [tcpdump](/api-platform/troubleshoot/diagnostic-tools-and-logs#tcpippacketsniffertcpdumputility).

  * If there are multiple IP addresses for backend server/Message Processor, then
  you need to try another `tcpdump` command usage.  Refer to [tcpdump](/api-platform/troubleshoot/diagnostic-tools-and-logs#tcpippacketsniffertcpdumputility) for more information about this tool and
  for other variants of this command.


1.  Analyze the TCP/IP packets using the [Wireshark tool](https://www.wireshark.org/)
or a similar tool. The following screenshot shows TCP/IP packets in Wireshark. 

  <img alt="" class="screenshot" src="/api-platform/images/502-bad-gateway-timout-1.png" width="95%">

1.  Notice in the Wireshark output, that the three-way TCP handshake completes
successfully in the first 3 packets. 
1.  The Message Processor then sends the "Client Hello" message in packet #4.
1.  Because there is no acknowledgement from the backend server, the Message
Processor retransmits the "Client Hello" message multiple times in packets 5,
6 and 7 after waiting for a predefined interval of time.
1.  When the Message Processor doesn't receive any acknowledgement after 3 retries,
it sends the **FIN, ACK** message to backend server to indicate that it is closing the connection.
1.  As you shown in the example Wireshark session, the connection to the backend
is successful (step #1), however, the SSL handshake timed out because the backend
server never responded.

If you performed the troubleshooting steps in this playbook and determined that a
timeout caused the TLS/SSL handshake error, go to the [Resolution](#resolution) section. 


### Using API Monitoring to identify a problem

Note: The Apigee Edge API Monitoring feature is available to Public Cloud users only.

[API Monitoring](/api-monitoring) enables you to isolate
problem areas quickly to diagnose error, performance, and latency issues and their
source, such as developer apps, API proxies, backend targets, or the API platform.

[Step through a sample scenario](/api-monitoring/sample-scenarios)
that demonstrates how to troubleshoot 5xx issues with your APIs using API Monitoring.
For example, you may want to set up an alert to be notified when the number of
**messaging.adaptors.http.BadGateway** faults exceeds a particular threshold.


## Resolution

Typically the SSL handshake timeouts occur because of firewall restrictions on
the backend server that blocks the traffic from Apigee Edge. If you followed
the diagnostic steps and determined that the cause of the handshake error is a
timeout, you need to engage your network team to identify the cause and fix the
firewall restrictions.

Note that the firewall restrictions could be imposed at different network layers.
It's important to make sure that the restrictions at all network layers are removed pertaining
to the Message Processor IPs to ensure smooth traffic flow between Apigee Edge
and the backend server.

If there are no firewall restrictions and/or the problem still persists, go to 
[Must Gather Diagnostic Information](#must_gather_diagnostic_information).


## Must Gather Diagnostic Information

If the problem persists even after following the above instructions, please gather the following diagnostic information. Contact and share them to [Apigee Support](https://support.apigee.com/):



1.  If you are a Public Cloud user, then provide the following information:
    1.  Organization Name
    1.  Environment Name
    1.  API Proxy Name
    1.  Complete curl command to reproduce the error
    1.  Trace File showing the error
    1.  TCP/IP packets captured on the backend server
1.  If you are a Private Cloud user, provide the following information:
    1.  Complete Error Message observed
    1.  API Proxy bundle
    1.  Trace File showing the error
    1.  Message Processor logs /opt/apigee/var/log/edge-message-processor/logs/system.log
    1.  TCP/IP packets captured on the backend server or Message Processor.
1.  Details about what sections in this Playbook you have tried and any other insights that will help us to fastrack resolution of this issue.

{% endblock %}
