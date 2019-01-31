{% extends "_base.html" %} {% block title %}Key Value Map Operations runtime error troubleshooting{% endblock %} {% block body %}

## UnsupportedOperationException


### Error code


```
java.lang.UnsupportedOperationException
```



### Error response body


```
{  
   "fault":{  
      "faultstring":"java.lang.UnsupportedOperationException",
      "detail":{  
         "errorcode":"Internal Server Error"
      }
   }
}
```



### Cause

This error occurs if the `mapIdentifier` attribute is set to an empty string in the Key Value Map Operations policy.


### Diagnosis



1.  Examine all the Key Value Map Operations policies in the specific API Proxy where the failure has occurred. If there is any Key Value Map Operations policy in which the `mapIdentifier` attribute is set as an empty string, then that's the cause of the error.


  For example, the following Key Value Map Operations policy has an empty `mapIdentifier`:


  ```
  <KeyValueMapOperations async="false" continueOnError="false" enabled="true" name="StoreKvm" mapIdentifier="">
      <DisplayName>StoreKvm</DisplayName>
      <Properties/>
      <ExclusiveCache>false</ExclusiveCache>
      <ExpiryTimeInSecs>300</ExpiryTimeInSecs>
      <Put override="true">
          <Key>
              <Parameter ref="request.queryparam.key"/>
          </Key>
          <Value ref="request.queryparam.value"/>
      </Put>
      <Scope>apiproxy</Scope>
  </KeyValueMapOperations>
  ```



### Resolution

Ensure the attribute `mapIdentifier` is set to a valid key value map in the Key Value Map Operations policy.

To correct the example policy shown above, you can specify the `mapIdentifier` to an existing key value map `UserLocationMap`.


```
<KeyValueMapOperations async="false" continueOnError="false" enabled="true" name="StoreKvm" mapIdentifier="UserLocationMap">
    <DisplayName>StoreKvm</DisplayName>
    <Properties/>
    <ExclusiveCache>false</ExclusiveCache>
    <ExpiryTimeInSecs>300</ExpiryTimeInSecs>
    <Put override="true">
        <Key>
            <Parameter ref="request.queryparam.key"/>
        </Key>
        <Value ref="request.queryparam.value"/>
    </Put>
    <Scope>apiproxy</Scope>
</KeyValueMapOperations>
```




## SetVariableFailed


### Error code

```
steps.keyvaluemapoperations.SetVariableFailed
```

### Error response body

<pre>
{  
   "fault":{  
      "faultstring":"Failed to set variable <var>variable_name</var> in KeyValueMapStepDefinition <var>policy_name</var>",
      "detail":{  
         "errorcode":"steps.keyvaluemapoperations.SetVariableFailed"
      }
   }
}
</pre>



### Example Error Message



```
{
   "fault":{  
      "faultstring":"Failed to set variable myvar in KeyValueMapStepDefinition EncryptedKVM",
      "detail":{  
         "errorcode":"steps.keyvaluemapoperations.SetVariableFailed"
      }
   }
}
```



### Cause

This error occurs if you try to retrieve a value from an encrypted key value map
and set the value to a variable whose name does not have the prefix `private.`. The
prefix, which is required for basic security purposes during debugging, hides the
encrypted values from API proxy Trace and debug sessions.


### Diagnosis



1.  
Identify the Key Value Map Operations policy where the error occurred and the name of the variable to which the value cannot be set. You can find both of these items in the `faultstring` element of the error response. For example, in the following `faultstring`, the policy name is `EncryptedKVM` and the variable is `myvar`:



  ```
  "faultstring":"Failed to set variable myvar in KeyValueMapStepDefinition EncryptedKVM"
  ```



1.  
In the failed Key Value Map Operations policy XML, verify that the name of the variable specified in the **<code>assignTo</code>** attribute of the `<Get>` element matches the variable name identified in the fault string (step #1 above). For example, the following Key Value Map Operations policy specifies a variable named <code>myvar</code> in the `<Get>` element, which matches what's in the `faultstring`:


  ```
  <KeyValueMapOperations async="false" continueOnError="false" enabled="true" name="Key-Value-Map-Operations-1" mapIdentifier="testEncrypted">
      <DisplayName>KeyValueMapOperations-1</DisplayName>
      <Properties/>
      <ExclusiveCache>false</ExclusiveCache>
      <ExpiryTimeInSecs>300</ExpiryTimeInSecs>
      <Get assignTo="myvar" index="1">
          <Key>
              <Parameter>foo</Parameter>
          </Key>
      </Get>
      <Scope>environment</Scope>
  </KeyValueMapOperations>
  ```

1.  Check if the key value map specified in the `mapIdentifier` attribute is an encrypted KVM.
If the KVM is encrypted at the environment scope, you can verify that it is encrypted
in the Edge UI. Otherwise, if it was created at organization or apiproxy scope, you need
to use the Management API to verify if it is encrypted. See also [Managing and using KVMs](/api-platform/cache/key-value-maps#managing).

  To check if an environment scoped KVM is encrypted in the Edge UI:
    1. In the Edge UI, click **Admin** in the left navigation menu.
    1. Click **Environments**.
    1. Click **Key Value Maps**.
    1. Find the Key Value Map you want to use.
    1. Click  the arrow to the left of the name to expand the list of values.
    1. Encrypted Key Value Maps display values masked with asterisks in the UI (*****) as shown in [About Encrypted KVMs](https://docs.apigee.com/api-platform/cache/key-value-maps#aboutencrypted).
  
  To check if a KVM scoped at the organization, environment, or apiproxy level is encrypted, use the
  [Management APIs](/api-reference/content/keyvalue-maps-management-api). If you use the
  API, a sample response that shows both the encrypted setting and asterisks for the value looks like this:
  
  ```
  {
    "encrypted": true,
    "entry": [
      {
        "name": "foo",
        "value": "*****"
      }
    ],
    "name": "encrypted"
  }
  ```


1.  If the key value map is an encrypted KVM and the variable name used in the 
`assignTo` attribute of the `<Get>` element does not have the prefix `private`.
Then that's the cause for the error.

  In the example Key Value Map Operations policy shown above, the key value map `testEncrypted` is encrypted and the variable name `myvar` does not have the prefix `private`. Hence you receive the error code:


  ```
  steps.keyvaluemapoperations.SetVariableFailed
```



### Resolution

Ensure that the variable name has the prefix `private.` in the `assignTo` attribute
of the `<Get>` element in Key Value Map Operations policy if the key value map
used is  encrypted.

To correct the example Key Value Map Operations policy shown above, you can modify
the value of the attribute `assignTo` to `private.myvar`:


```
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<KeyValueMapOperations async="false" continueOnError="false" enabled="true" name="Key-Value-Map-Operations-1" mapIdentifier="testEncrypted">
    <DisplayName>KeyValueMapOperations-1</DisplayName>
    <Properties/>
    <ExclusiveCache>false</ExclusiveCache>
    <ExpiryTimeInSecs>300</ExpiryTimeInSecs>
    <Get assignTo="private.myvar" index="1">
        <Key>
            <Parameter>foo</Parameter>
        </Key>
    </Get>
    <Scope>environment</Scope>
</KeyValueMapOperations>
```

{% endblock %}
