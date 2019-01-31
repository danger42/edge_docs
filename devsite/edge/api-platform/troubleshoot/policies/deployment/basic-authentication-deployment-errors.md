{% extends "_base.html" %} {% block title %}BasicAuthentication policy deployment error troubleshooting{% endblock %} {% block body %}

<aside class="note">Was this troubleshooting playbook helpful? Please let us know
  by clicking <a href="" class="google-feedback"
   data-p="1636213" data-b="docsite" data-context="troubleshooting-feedback">
  Send Feedback 
</a>.</aside>



## UserNameRequired

### Error message

Deployment of the API proxy through either the Edge UI or Edge management API fails with this error message:

<pre class="prettyprint">
Error Deploying Revision <var>revision_number</var> to <var>env_name</var>
BasicAuthenticationPolicy: Username element must be present for <var>operation</var> operation.
</pre>

**Example error message**

```
Error Deploying Revision 2 to test
BasicAuthenticationPolicy: Username element must be present for Encode operation.
```

**Example screenshot**

<img src="/api-platform/images/ba-usernamerequired.png">

### Cause

The deployment of the API Proxy fails if the `<User>` element is not defined in the [BasicAuthentication policy](/api-platform/reference/policies/basic-authentication-policy). The `<User>` element is mandatory for both Encode and Decode operations. 

<aside class="note">For Encoding operation, the <code>&lt;User&gt;</code> element is used to specify the variable containing the username. For the Decoding operation, the <code>&lt;User&gt;</code> element is used to specify the variable where the decoded username is written.</aside>

### Diagnosis

1.  Examine all the BasicAuthentication policies used in the API proxy. If there is any policy where the `<User>` element is not specified, then that's the cause of the error.  The `<User>` element is mandatory in BasicAuthentication policy for Encode or Decode operations. 

    The sample BasicAuthentication policy shown below is used for Encode operation, but doesn't have the `<User>` element defined:

  ```
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<BasicAuthentication name="ApplyBasicAuthHeader">
    <DisplayName>ApplyBasicAuthHeader</DisplayName>
    <Operation>Encode</Operation>
    <IgnoreUnresolvedVariables>false</IgnoreUnresolvedVariables>
    <Password ref="BasicAuth.credentials.password"/>
    <AssignTo createNew="false">request.header.Authorization</AssignTo>
</BasicAuthentication>
  ```

    The deployment fails with the below error:

  ```
BasicAuthenticationPolicy: Username element must be present for Encode operation.
  ```


### Resolution

Ensure that the `<User>` element is defined within the BasicAuthentication policy for Encode or Decode operation. 

To correct the example shown above, include the <`User> `element within the policy as shown below:

```
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<BasicAuthentication name="ApplyBasicAuthHeader">
    <DisplayName>ApplyBasicAuthHeader</DisplayName>
    <Operation>Encode</Operation>
    <IgnoreUnresolvedVariables>false</IgnoreUnresolvedVariables>
    <User ref="BasicAuth.credentials.username"/>
    <Password ref="BasicAuth.credentials.password"/>
    <AssignTo createNew="false">request.header.Authorization</AssignTo>
</BasicAuthentication>
```

## PasswordRequired

### Error message

Deployment of the API proxy through either the Edge UI or Edge management API fails with this error message:

<pre class="prettyprint">
Error Deploying Revision <var>revision_number</var> to <var>env_name</var>
BasicAuthenticationPolicy: Password element must be present for <var>operation</var> operation.
</pre>

**Example error message**

```
Error Deploying Revision 2 to test
BasicAuthenticationPolicy: Password element must be present for Encode operation.
```

**Example screenshot**

<img src="/api-platform/images/ba-passwordrequired.png">


### Cause

The deployment of the API Proxy fails if the `<Password>` element is not defined in the [BasicAuthentication policy](https://docs.apigee.com/api-platform/reference/policies/basic-authentication-policy). The `<Password>` element is mandatory for both Encode and Decode operations. 

<aside class="note"> For Encoding operation, the <code>&lt;Password&gt;</code> element is used to specify the variable containing the password. For the Decoding operation, the <code>&lt;Password&gt;</code> element is used to specify the variable where the decoded password is written.</aside>


### Diagnosis

1.  Examine all the BasicAuthentication policies used in the API proxy. If there is any policy where the `<Password>` element is not defined, then that's the cause of the error.  The `<Password>` element is mandatory in the BasicAuthentication policy for Encode or Decode operation. 

    The sample BasicAuthentication policy shown below is used for Encode operation, but doesn't have the `<Password>` element defined:

  ```
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<BasicAuthentication name="ApplyBasicAuthHeader">
    <DisplayName>ApplyBasicAuthHeader</DisplayName>
    <Operation>Encode</Operation>
    <IgnoreUnresolvedVariables>false</IgnoreUnresolvedVariables>
    <User ref="BasicAuth.credentials.username"/>
    <AssignTo createNew="false">request.header.Authorization</AssignTo>
</BasicAuthentication>
  ```

    The deployment fails with the below error:

  ```
BasicAuthenticationPolicy: Password element must be present for Encode operation.
  ```

### Resolution

Ensure that the `<Password>` element is defined within the BasicAuthentication policy for Encode or Decode operation. 

To correct the example shown above, include the <`Password> `element within the policy as shown below:

```
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<BasicAuthentication name="ApplyBasicAuthHeader">
    <DisplayName>ApplyBasicAuthHeader</DisplayName>
    <Operation>Encode</Operation>
    <IgnoreUnresolvedVariables>false</IgnoreUnresolvedVariables>
    <User ref="BasicAuth.credentials.username"/>
    <Password ref="BasicAuth.credentials.password"/>
    <AssignTo createNew="false">request.header.Authorization</AssignTo>
</BasicAuthentication>
```


## AssignToRequired

### Error message

Deployment of the API proxy through either the Edge UI or Edge management API fails with this error message:

<pre class="prettyprint">
Error Deploying Revision <var>revision_number</var> to <var>env_name</var>
BasicAuthenticationPolicy: AssignTo element must be present for <var>operation</var> operation.
</pre>

**Example error message**

```
Error Deploying Revision 2 to test
BasicAuthenticationPolicy: AssignTo element must be present for Encode operation.
```

**Example screenshot**

<img src="/api-platform/images/ba-assigntorequired.png">


### Cause

The deployment of the API Proxy fails if the `<AssignTo>` element is not defined in the [BasicAuthentication policy](https://docs.apigee.com/api-platform/reference/policies/basic-authentication-policy). The `<AssignTo>` element is mandatory for both Encode and Decode operations.

<aside class="note"> The <code>&lt;AssignTo&gt;</code> element  specifies the target variable to set with the encoded or decoded value generated by the BasicAuthentication policy.</aside>

### Diagnosis

1.  Examine all the BasicAuthentication policies used in the API proxy. If there is any policy where the element `<AssignTo>` is not defined, then that's the cause of the error. The `<AssignTo>` element is mandatory to be defined in BasicAuthentication policy for both Encode and Decode operations. 

    The sample BasicAuthentication policy shown below is used for Encode operation, but doesn't have the `<AssignTo>` element defined:

  ```
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<BasicAuthentication name="ApplyBasicAuthHeader">
    <DisplayName>ApplyBasicAuthHeader</DisplayName>
    <Operation>Encode</Operation>
    <IgnoreUnresolvedVariables>false</IgnoreUnresolvedVariables>
    <User ref="BasicAuth.credentials.username"/>
    <Password ref="BasicAuth.credentials.password"/>
</BasicAuthentication>
 ```
    The deployment fails with the below error:

  ```
BasicAuthenticationPolicy: AssignTo element must be present for Encode operation.
  ```

### Resolution

Ensure that the `<AssignTo>` element is defined within the BasicAuthentication policy for Encode or Decode operation. 

To correct the example shown above, include the <`AssignTo> `element within the policy as shown below:


```
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<BasicAuthentication name="ApplyBasicAuthHeader">
    <DisplayName>ApplyBasicAuthHeader</DisplayName>
    <Operation>Encode</Operation>
    <IgnoreUnresolvedVariables>false</IgnoreUnresolvedVariables>
    <User ref="BasicAuth.credentials.username"/>
    <Password ref="BasicAuth.credentials.password"/>
    <AssignTo createNew="false">request.header.Authorization</AssignTo>
</BasicAuthentication>
```



## SourceRequired

### Error message

Deployment of the API proxy through either the Edge UI or Edge management API fails with this error message:

<pre class="prettyprint">
Error Deploying Revision <var>revision_number</var> to <var>env_name</var>
BasicAuthenticationPolicy: Source element must be present for Decode operation.
</pre>

**Example error message**

```
Error Deploying Revision 2 to test
BasicAuthenticationPolicy: Source element must be present for Decode operation.
```

**Example screenshot**

<img src="/api-platform/images/ba-sourcerequired.png">


### Cause

The deployment of the API Proxy fails if the `<Source>` element is not defined in the BasicAuthentication policy that is used for decoding operation. The  `<Source>` element is mandatory if the `<Operation>` is specified as `Decode` in the BasicAuthentication policy.

<aside class="note"> The <code>&lt;Source&gt;</code> element specifies the Base64 encoded string that needs to be decoded.</aside>

### Diagnosis

1.  Examine all the BasicAuthentication policies used in the API proxy. If there is any policy where the element `<Source>` is not defined and the `<Operation>` element is defined as `Decode`, then that's the cause of the error. The  `<Source>` element is mandatory if the `<Operation>` is specified as `Decode` in the BasicAuthentication policy.

    The sample BasicAuthentication policy shown below is used for `Decode` operation, but doesn't have the `<Source>` element defined:

  ```
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<BasicAuthentication name="ApplyBasicAuthHeader">
    <DisplayName>ApplyBasicAuthHeader</DisplayName>
    <Operation>Decode</Operation>
    <IgnoreUnresolvedVariables>false</IgnoreUnresolvedVariables>
    <User ref="BasicAuth.credentials.username"/>
    <Password ref="BasicAuth.credentials.password"/>
    <AssignTo createNew="false">request.header.Authorization</AssignTo>
</BasicAuthentication>
  ```

    The deployment fails with the below error:

  ```
BasicAuthenticationPolicy: Source element must be present for Decode operation.
  ```

### Resolution

Ensure that the `<Source>` element is defined in BasicAuthentication policy when it is used for `Decode` operation.

To correct the example shown above, include the <`Source> `element within the policy as shown below:

```
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<BasicAuthentication name="ApplyBasicAuthHeader">
    <DisplayName>ApplyBasicAuthHeader</DisplayName>
    <Operation>Decode</Operation>
    <IgnoreUnresolvedVariables>false</IgnoreUnresolvedVariables>
    <User ref="BasicAuth.credentials.username"/>
    <Password ref="BasicAuth.credentials.password"/>
    <AssignTo createNew="false">request.header.Authorization</AssignTo>
    <Source>request.header.Authorization</Source>
</BasicAuthentication>
```

{% endblock %}

