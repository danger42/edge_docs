{% extends "_base.html" %} {% block title %}AccessControl policy runtime error troubleshooting{% endblock %} {% block body %}

<aside class="note">Was this troubleshooting playbook helpful? Please let us know
  by clicking <a href="" class="google-feedback"
   data-p="1636213" data-b="docsite" data-context="troubleshooting-feedback">
  Send Feedback
</a>.</aside>


## IPDeniedAccess


### Error code


```
steps.accesscontrol.IPDeniedAccess
```



### Error response body


<pre class="prettyprint">
{
    "fault": {
        "faultstring": "Access Denied for client ip : <var>client_IP</var>",
        "detail": {
            "errorcode": "accesscontrol.IPDeniedAccess"
        }
    }
}
</pre>


### Example error message


```
{
    "fault": {
        "faultstring": "Access Denied for client ip : 104.132.196.83",
        "detail": {
            "errorcode": "accesscontrol.IPDeniedAccess"
        }
    }
}
```



### Cause

This error occurs if the client IP address, or any IP address passed as part of the X-Forwarded-For HTTP header in the API request, matches any IP address(es) specified in the `<SourceAddress>` element within the `<MatchRule>` element of the Access Control Policy, and the `action` attribute of the `<MatchRule>` element is set to `DENY`.

For example, let's say the `<SourceAddress>` is defined as shown below:


```
<SourceAddress mask="32">104.132.196.83</SourceAddress>
```


If the above IP address matches the IP address of the client system (indicated by the variable `proxy.client.ip`), or any of the IP addresses passed as part of the X-Forwarded-For HTTP header in the API request, the error will occur.


### Diagnosis



1.  Identify the IP address(es) denied access for a specific API request. You can find this information in the `faultstring` element of the error response.

    For example, in the following `faultstring`, the IP address is `104.132.196.83:`


    ```
    "faultstring": "Access Denied for client ip : 104.132.196.83"
    ```


1.  Examine all the Access Control policies in the failed API Proxy and determine the specific policy where the IP address(es) specified in the `<SourceAddress>` element matches the IP address(es) identified in the `faultstring` (Step 1 above).

    For example, the following policy defines the `<SourceAddress>` IP as `104.132.196.83,` which matches what is in the `faultstring`:



  ```
  <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
      <AccessControl async="false" continueOnError="false" enabled="true" name="Access-Control">
      <DisplayName>Access-Control</DisplayName>
      <Properties/>
      <IPRules noRuleMatchAction="ALLOW">
          <MatchRule action="DENY">
              <SourceAddress mask="32">104.132.196.83</SourceAddress>
          </MatchRule>
      </IPRules>
      </AccessControl>
  ```



1.  Determine the IP address(es) from which the API request was made. This can be done in multiple ways:
    1.  Using UI Trace
        1.  Capture the trace for the failing API request.
        1.  Select the specific Access Control Policy that has failed from the right hand panel.
        1.  Check the value of the variable `proxy.client.ip` as shown in the following screenshot of a sample trace.

          <img class="screenshot" width="80%" alt="" src="/api-platform/images/access-control-runtime-error.png">


        1.  If the `proxy.client.ip` is not listed, then check the value of the HTTP header X-Forwarded-For.


    1.  Using Custom Reports


          You can create Custom Reports to determine whether a 403 status code was thrown during the execution of the Access Control policy within the API proxy and also determine the client IP address. This is particularly useful if the issue has occurred in the past or if the issue is intermittent and you are unable to capture the trace in UI.


          Refer to [Create and manage custom reports](https://docs.apigee.com/api-platform/analytics/create-custom-reports) to understand how to create custom reports. In the custom report, choose:


          1.	**Sum of Traffic** as a Metric, and


          1.	**Proxy**, **Response Status Code**, **Proxy Client IP** and **X-Forwarded-For** as Dimensions.



      This should help you to determine the client IP or IP addresses passed as part of HTTP Header X-Forwarded-For that led to the error.

1.  If the client IP address (indicated by the variable `proxy.client.ip`), or any IP address passed as part of the X-Forwarded-For HTTP header in the API request, matches the IP address(es) specified in the `<SourceAddress>` element within the `<MatchRule>` element of Access Control Policy, where the `action` attribute is set to `DENY`, then that is the cause of the error.

    In the example shown above, the value set in the reference variable `proxy.client.ip` (as seen in the screenshot of the trace above) matches the IP address defined in the `<SourceAddress>` element of the Access Control policy, thus triggering the error response:


    ```
    "faultstring": "Access Denied for client ip : 104.132.196.83"

    ```



### Resolution

If the Access Control policy is intended to deny access to API requests coming from the specific IP address(es) listed in the `faultstring`, then the error message is expected. In this case, there is no additional action is required.

However, if you determine that the specific IP address(es) can be given access to the API requests for the specific API Proxy, then modify the Access Control Policy to allow access to those IP address(es). Alternatively, you can remove the Access Control Policy from the API Proxy if you don't want to deny access to any IP address(es).

Here is an example that shows how to allow access to a specific IP Address `104.132.196.83` only and deny access for the rest:


```
<AccessControl name="ACL">
  <IPRules noRuleMatchAction = "DENY">
    <MatchRule action = "ALLOW">
      <SourceAddress mask="32">104.132.196.83</SourceAddress>
    </MatchRule>
  </IPRules>
</AccessControl>
```
{% endblock %}
