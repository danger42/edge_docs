{% extends "_base.html" %} {% block title %}Service Callout policy deployment error troubleshooting{% endblock %} {% block body %}

<aside class="note">Was this troubleshooting playbook helpful? Please let us know
  by clicking <a href="" class="google-feedback"
   data-p="1636213" data-b="docsite" data-context="troubleshooting-feedback">
  Send Feedback 
</a>.</aside>


## URLMissing




### Error message

Deployment of the API proxy through either the Edge UI or Edge management API fails with this error message: 



<pre>
Error Saving Revision [<b><i>revision_number</b></i>]
URL is missing in Step [<b><i>policy_name</b></i>]
</pre>


**Example error message**


```
Error Saving Revision 2
URL is missing in Step ExecuteGeocodingRequest.
```


**Example screenshot**



<img alt="" src="/api-platform/images/sc-deploy-error-2.png">



### Cause

If the `<URL>` element in the Service Callout policy is missing or left blank, then the deployment of the API proxy fails.


### Diagnosis

Examine the `<URL>` element in the Service Callout policy named in the error message. If there is no URL declared within the element, then that is the cause of the error. For example, the following Service Callout policy has an empty `<URL>` element:


```
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<ServiceCallout name="ExecuteGeocodingRequest">
    <Request variable="GeocodingRequest"/>
    <Response>GeocodingResponse</Response>
    <HTTPTargetConnection>
        <URL></URL>
    </HTTPTargetConnection>
</ServiceCallout>
```


Because the `<URL>` element is empty, the deployment of the API proxy fails.


### Resolution

Ensure that the `<URL>` element in the Service Callout policy has a valid URL. For example:


```
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<ServiceCallout name="ExecuteGeocodingRequest">
    <Request variable="GeocodingRequest"/>
    <Response>GeocodingResponse</Response>
    <HTTPTargetConnection>
        <URL>http://maps.googleapis.com/maps/api/geocode/json</URL>
    </HTTPTargetConnection>
</ServiceCallout>
```




## ConnectionInfoMissing



### Error message

Deployment of the API proxy through either the Edge UI or Edge management API fails with this error message:


<pre>
Error Saving Revision [<b><i>revision_number</b></i>]
Connection information is missing in Step [<b><i>policy_name</b></i>]
</pre>


**Example error message**


```
Error Saving Revision 1
Connection information is missing in Step ExecuteGeocodingRequest.
```


**Example screenshot**





<img alt="" src="/api-platform/images/sc-deploy-error-3.png">



### Cause

If the Service Callout Policy does not have either the `<HTTPTargetConnection>` or `<LocalTargetConnection>` element, then the deployment of the API proxy fails.


### Diagnosis

Determine whether the Service Callout policy has `<HTTPTargetConnection>` or `<LocalTargetConnection>` element defined. For example:


```
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<ServiceCallout name="ExecuteGeocodingRequest">
    <Request variable="GeocodingRequest"/>
    <Response>GeocodingResponse</Response>
</ServiceCallout>
```


Notice that there is no  `<HTTPTargetConnection>` or `<LocalTargetConnection>` element defined in the policy.


### Resolution

Ensure that the Service Callout policy has either the `<HTTPTargetConnection>` or `<LocalTargetConnection>` element. For example:


```
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<ServiceCallout name="ExecuteGeocodingRequest">
    <Request variable="GeocodingRequest"/>
    <Response>GeocodingResponse</Response>
    <HTTPTargetConnection>
        <URL>http://maps.googleapis.com/maps/api/geocode/json</URL>
    </HTTPTargetConnection>
</ServiceCallout>
```



## InvalidTimeoutValue



### Error message

Deployment of API proxy through either the Edge UI or Edge management API fails with this error message: 



<pre>
Error Saving Revision [<b><i>revision_number</b></i>]
Invalid Timeout value [<b><i>0 or negative_number</b></i>].
</pre>


**Example error message**


```
Error Saving Revision 1
Invalid Timeout value -1.
```


**Example screenshot**

<img alt="" src="/api-platform/images/sc-deploy-error-1.png">



### Cause

If the `<Timeout>` element in the Service Callout policy has a **zero or negative value** specified, then the deployment of the API proxy fails.


### Diagnosis

Examine the `<Timeout>` element in the Service Callout policy. If the value is zero or a negative number, then that is the cause for the error. For example:


```
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<ServiceCallout name="ExecuteGeocodingRequest">
    <Request variable="GeocodingRequest"/>
    <Response>GeocodingResponse</Response>
    <Timeout>0</Timeout>
    <HTTPTargetConnection>
        <URL>http://maps.googleapis.com/maps/api/geocode/json</URL>
    </HTTPTargetConnection>
</ServiceCallout>
```



Because the `<Timeout>` element has a value of zero, the proxy fails to deploy. 


### Resolution

Ensure that the value specified for the `<Timeout>` element is a non-zero or non-negative number. For example:

```
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<ServiceCallout name="ExecuteGeocodingRequest">
    <Request variable="GeocodingRequest"/>
    <Response>GeocodingResponse</Response>
    <Timeout>10</Timeout>
    <HTTPTargetConnection>
        <URL>http://maps.googleapis.com/maps/api/geocode/json</URL>
    </HTTPTargetConnection>
</ServiceCallout>
```
{% endblock %}
