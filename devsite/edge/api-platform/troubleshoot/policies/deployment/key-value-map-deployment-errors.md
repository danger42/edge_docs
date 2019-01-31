{% extends "_base.html" %} {% block title %}Key Value Map Operations policy deployment error troubleshooting{% endblock %} {% block body %}

## InvalidIndex


### Error Message

Deployment of the API proxy through either the Edge UI or Edge management API fails with this error message:


<pre>
Error Saving Revision <var>revision_number</var>
Invalid index <var>index</var> in KeyValueMapStepDefinition <var>policy_name</var>.
</pre>



### Example Error Message


```
Error Saving Revision 2
Invalid index 0 in KeyValueMapStepDefinition GetKVM.
```



### Example Error Screenshot



<img alt="" src="/api-platform/images/kvm-deploy-error-3.png" width="75%">



### Cause

If the `index` attribute specified in the `<Get>` element of a Key Value Map Operations policy is zero or a negative number, then the deployment of the API proxy fails. The index starts from `1`, so an index of zero or negative integer is considered as invalid.

For example, if the `index` specified is `0` in the `<Get>` element of a Key Value Map Operations policy, then the deployment of the API proxy fails.


### Diagnosis



1.  Identify the Key Value Map Operations policy where the error occurred and the invalid index. You can find this information from the error message. For example, in the following error, the policy name is `GetKVM` and the invalid index is `0`:

	

  <code>Invalid index <strong><em>0</em></strong> in KeyValueMapStepDefinition <em>GetKVM</em>.</code>



1.  Verify that the value of the index specified in the `<Get>` element of the failed Key Value Map Operations policy matches with the value identified in the error message (step #1 above). For example, the following policy specifies the value of the index as `0`, which matches what's in the error message:

    ```
    <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
    <KeyValueMapOperations mapIdentifier="FooKVM" async="false" continueOnError="false" enabled="true" name="GetKVM">
        <DisplayName>GetKVM</DisplayName>
        <ExpiryTimeInSecs>86400</ExpiryTimeInSecs>
        <Scope>environment</Scope>
        <Get assignTo="foo_variable" index="0">
            <Key>
                <Parameter>FooKey_1</Parameter>
            </Key>
        </Get>
    </KeyValueMapOperations>
    ```


1.  If the index specified is zero or negative integer, then that's the cause of the error.

    In the example Key Value Map Operations policy shown above, the value of the index is `0`, which is invalid. Therefore, the deployment of the API Proxy fails with the error:



  ```
  Invalid index 0 in KeyValueMapStepDefinition GetKVM.
  ```



### Resolution

Ensure that index attribute specified in the `<Get>` element of Key Value Map Operations policy is valid (non-zero or negative integer).

To correct the example Key Value Map Operations policy shown above, you could modify the index to `1`.


```
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<KeyValueMapOperations mapIdentifier="FooKVM" async="false" continueOnError="false" enabled="true" name="GetKVM">
    <DisplayName>GetKVM</DisplayName>
    <ExpiryTimeInSecs>86400</ExpiryTimeInSecs>
    <Scope>environment</Scope>
    <Get assignTo="foo_variable" index="1">
        <Key>
            <Parameter>FooKey_1</Parameter>
        </Key>
    </Get>
</KeyValueMapOperations>
```


## KeyIsMissing

### Error Message

Deployment of the API proxy through either the Edge UI or Edge management API fails with this error message:

<pre>
Error Saving Revision <var>revision_number</var>
Error occurred while validation of bean <var>policy_name</var>.xml
Reason: - Non null value expected for element Parameter in Entry.
</pre>


### Example Error Message


```
Error Saving Revision 3
Error occurred while validation of bean GetKVM.xml. Reason: - Non null value expected for element Parameter in Entry
```



### Example Error Screenshot


<img alt="" src="/api-platform/images/kvm-deploy-error-2.png">




### Cause

This error occurs if the `<Key>` element is completely missing or `<Parameter>`
element is missing within `<Key>` element underneath the `<Entry>` of the `<InitialEntries>`
element of the Key Value Map Operations policy.


### Diagnosis



1.  Identify the Key Value Map Operations policy where the error occurred. You can find this information from the error message.  For example, in the following error the name of the Key Value Map Operations policy is `GetKVM`.

    ```
    Error occurred while validation of bean GetKVM.xml. Reason: - Non null value expected for element Parameter in Entry
    ```


1.  In the failed Key Value Map Operations policy, check if there is any `<Entry>`
element which has a missing `<Key>` or `<Parameter>` element underneath the `<InitialEntries>`.

    Here's a sample Key Value Map Operations policy that has missing `<Parameter>`
    element within `<Key>` element:


    ```
    <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
    <KeyValueMapOperations async="false" continueOnError="false" enabled="true" name="GetKVM" mapIdentifier="FooKVM">
        <DisplayName>GetKVM</DisplayName>
        <Properties/>
        <ExclusiveCache>false</ExclusiveCache>
        <ExpiryTimeInSecs>300</ExpiryTimeInSecs>
        <InitialEntries>
            <Entry>
                <Key/>
                <Value>v1</Value>
            </Entry>
            <Entry>
                <Key>
                    <Parameter>k2</Parameter>
                </Key>
                <Value>v2</Value>
            </Entry>
        </InitialEntries>
    ```

    In this case the first element is causing the issue.


### Resolution

Ensure that all the `<Entry>` elements underneath the `<InitialEntries>` element in the Key Value Map Operations policy have a `<Key>` element followed by a `<Parameter>` element.

To correct the example Key Value Map Operations policy shown above, adding a `<Parameter>` element solves the problem:


```
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<KeyValueMapOperations async="false" continueOnError="false" enabled="true" name="GetKVM" mapIdentifier="FooKVM">
    <DisplayName>GetKVM</DisplayName>
    <Properties/>
    <ExclusiveCache>false</ExclusiveCache>
    <ExpiryTimeInSecs>300</ExpiryTimeInSecs>
    <InitialEntries>
        <Entry>
            <Key>
                <Parameter>k1</Parameter>
            </Key>
            <Value>v1</Value>
        </Entry>
        <Entry>
            <Key>
                <Parameter>k2</Parameter>
            </Key>
            <Value>v2</Value>
        </Entry>
    </InitialEntries>
    ...
```



## ValueIsMissing


### Error Message

Deployment of the API proxy through either the Edge UI or Edge management API fails with this error message:


<pre>
Error Saving Revision <var>revision_number</var>
Error occurred while validation of bean <var>policy_name</var>.xml. Reason: - Non null value expected for element Value in Entry.
</pre>



### Example Error Message


```
Error Saving Revision 3
Error occurred while validation of bean GetKVM.xml.Reason: - Non null value expected for element Value in Entry
```



### Example Error Screenshot



<img alt="" src="/api-platform/images/kvm-deploy-error-1.png">




### Cause

This error occurs if the `<Value>` element is missing underneath the `<Entry>` element of the `<InitialEntries>` element of the Key Value Map Operations policy.


### Diagnosis



1.  Identify the Key Value Map Operations policy where the error occurred. You can find this information from the error message.  For example, in the following error the name of the Key Value Map Operations policy is `GetKVM`.

    ```
    Error occurred while validation of bean GetKVM.xml. Reason: - Non null value expected for element Value in Entry
    ```


1.  In the failed Key Value Map Operations policy, check if there is any `<Entry>` element which has a missing `<Value>` element underneath the `<InitialEntries>`. 

    Here's a sample Key Value Map Operations policy that has missing `<Value>` element:


    ```
    <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
    <KeyValueMapOperations async="false" continueOnError="false" enabled="true" name="GetKVM" mapIdentifier="testNotEncrypte">
        <DisplayName>GetKVM3</DisplayName>
        <Properties/>
        <ExclusiveCache>false</ExclusiveCache>
        <ExpiryTimeInSecs>300</ExpiryTimeInSecs>
        <InitialEntries>
            <Entry>
                <Key>
                    <Parameter>k1</Parameter>
                </Key>
            </Entry>
            <Entry>
                <Key>
                    <Parameter>k2</Parameter>
                </Key>
                <Value>v2</Value>
            </Entry>
        </InitialEntries>
        ...

    ```



### Resolution

Ensure that all the `<Entry>` elements underneath the `<InitialEntries>` element in the Key Value Map Operations policy have a `<Value>` element.

To correct the example Key Value Map Operations policy shown above, adding a `<Value>` element solves the problem:


```
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<KeyValueMapOperations async="false" continueOnError="false" enabled="true" name="GetKVM" mapIdentifier="testNotEncrypte">
    <DisplayName>GetKVM3</DisplayName>
    <Properties/>
    <ExclusiveCache>false</ExclusiveCache>
    <ExpiryTimeInSecs>300</ExpiryTimeInSecs>
    <InitialEntries>
        <Entry>
            <Key>
                <Parameter>k1</Parameter>
            </Key>
            <Value>v1</Value>
        </Entry>
        <Entry>
            <Key>
                <Parameter>k2</Parameter>
            </Key>
            <Value>v2</Value>
        </Entry>
    </InitialEntries>
    ...
```
{% endblock %}
