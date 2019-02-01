Project: /_project.yaml
Book: /_book.yaml

# Report timed out

<aside class="note">Was this troubleshooting playbook helpful? Please let us know
  by clicking <a href="" class="google-feedback"
   data-p="1636213" data-b="docsite" data-context="troubleshooting-feedback">
  Send Feedback
</a>.</aside>

## Symptom

When you use the **[apigee-provision](/private-cloud/v4.18.05/creating-organization-environment-and-virtual-host)** script to create a new organization, sometimes the script exits with an error message. Following this error, if you you attempt to view the Edge UI Dashboard or any analytics dashboards, you will observe the error message **The report timed out** for the newly created organization.


## Error Message

When you run the **[apigee-provision](/private-cloud/v4.18.05/creating-organization-environment-and-virtual-host)** script to create a new organization, you may see the following error message:

```
!!!! Error !!!! 
HTTP STATUS CODE: 400 
{ 
"code" : "dataapi.service.PGFoundInMultipleGroups", 
"message" : "dataapi.service.PGFoundInMultipleGroups", 
"contexts" : [ ] 
} 
```

Even if you get this error, you can operate on the newly created organization after the provisioning script exits.  However, when you attempt to view the Edge UI Dashboard, you will observe the below error message for the newly created organization:

```
The report timed out
Try again with a smaller date range or a larger aggregation interval.
```

Here is a screenshot showing the error:

<img alt="" width="75%" src="/api-platform/images/IMG-report-timed-out-error.png">

## Possible Causes

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
   <td><a href="/api-platform/troubleshoot/analytics/report-timed-out#cause_multiple_ax_groups_setup">Multiple AX Groups setup</a>
   </td>
   <td>Multiple analytics groups have been created with the same set of Postgres servers.
   </td>
   <td>Edge Private Cloud Users
   </td>
  </tr>
</table>

## Cause: Multiple AX Groups setup

### Diagnosis

1.  Execute the following Analytics Groups management API and determine if the output shows more than one analytics group defined. For example:

  ```
  curl -u adminEmail:adminPwd http://<ms_ip>:8080/v1/analytics/groups/ax
  ```

  **Sample Output showing two analytics groups**

  <pre class="prettyprint">
    {  
     <b>"name":"axgroup-001"</b>,
     "properties":{  
     },
     "scopes":[  
     ],
     "uuids":{  
        "qpid-server":[  
           "5c1e9690-7b58-499a-a4bb-d54454474b8f",
           "7794c428-e553-4ed2-843d-69f93bbec8a3"
        ],
        "postgres-server":[  
           "3b28b790-ec4e-45c5-a8d0-6d6f2088da65:750cd8ba-1799-4dfb-8c74-548010e95e5e"
        ]
     },
     "consumer-groups":[  
        {  
           "name":"consumer-group-001",
           "consumers":[  
              "5c1e9690-7b58-499a-a4bb-d54454474b8f",
              "7794c428-e553-4ed2-843d-69f93bbec8a3"
           ],
           "datastores":[  
              "3b28b790-ec4e-45c5-a8d0-6d6f2088da65:750cd8ba-1799-4dfb-8c74-548010e95e5e"
           ],
           "properties":{
           }
        }
     ],
     "data-processors":{  
     }
  },
  {  
     <b>"name":"axgroup001"</b>,
     "properties":{  
        "consumer-type":"ax"
     },
     "scopes":[  
        "017pdspoint~dev",
        "010test~dev",
        "019charmo~dev",
        "009gcisearch1~dev",
        "000fj~trial-fjwan",
        "009dekura~dev",
        "008pisa~dev",
        "004fjadrms~dev",
        "018k5billing~dev",
        "004study14~dev",
        "001teama~dev",
        "005specdb~dev",
        "test~dev",
        "000fj~prod-fjwan",
        "012pjweb~dev",
        "020workflow~dev",
        "007ikou~prod-fjwan",
        "003asano~dev",
        "013mims~dev",
        "006studyhas~dev",
        "006efocus~dev",
        "002wfproto~dev",
        "008murahata~dev",
        "016mediaapi~dev",
        "015skillnet~dev",
        "014aclmanager~dev",
        "010fjppei~dev",
        "000fj~trial",
        "003esupport~dev",
        "000fj~prod",
        "005ooi~dev",
        "test~elb1",
        "007fjauth~dev",
        "011osp~dev",
        "002study~dev",
        "999test~dev"
     ],
     "uuids":{  
        "qpid-server":[  
           "5c1e9690-7b58-499a-a4bb-d54454474b8f",
           "7794c428-e553-4ed2-843d-69f93bbec8a3"
        ],
        "aries-datastore":[  
        ],
        "postgres-server":[  
           "3b28b790-ec4e-45c5-a8d0-6d6f2088da65:750cd8ba-1799-4dfb-8c74-548010e95e5e"
        ],
        "dw-server":[  
        ]
     },
     "consumer-groups":[  
        {  
           "name":"consumer-group-001",
           "consumers":[  
              "5c1e9690-7b58-499a-a4bb-d54454474b8f",
              "7794c428-e553-4ed2-843d-69f93bbec8a3"
           ],
           "datastores":[  
              "3b28b790-ec4e-45c5-a8d0-6d6f2088da65:750cd8ba-1799-4dfb-8c74-548010e95e5e"
           ],
           "properties":{  
           }
        }
     ],
     "data-processors":{  
     }
  }
  </pre>

  This output shows that there are two analytics groups **axgroup-001** and **axgroup001**.

1.  Check to make sure all of the analytics groups have scopes defined. 

  In the sample analytics groups output shown above, the analytics group **axgroup-001** does not have any scopes defined, but it still has Postgres servers defined as datastores.

1.  Execute the below Qpid queue stats command on the Qpid servers and validate if there are no messages coming for the specific analytics group identified in step #2. 

  ```
  qpid-stat -q
  ```

  **Sample Qpid queue stats** 
  
  The following Qpid queue stats indicate that there are no messages coming for the specific analytics group queue from the example cited above (axgroup-001):


  <table>
    <tr>
     <td>
  <strong>queue</strong>
     </td>
     <td><strong>dur</strong>
     </td>
     <td><strong>autoDel</strong>
     </td>
     <td><strong>excl</strong>
     </td>
     <td><strong>msg</strong>
     </td>
     <td><strong>msgIn</strong>
     </td>
     <td><strong>msgOut</strong>
     </td>
     <td><strong>bytes</strong>
     </td>
     <td><strong>bytesIn</strong>
     </td>
     <td><strong>bytesOut</strong>
     </td>
     <td><strong>cons</strong>
     </td>
     <td><strong>bind</strong>
     </td>
    </tr>
    <tr>
     <td>140995fe-71a7-4000-a1f4-71b7a951da7f:0.0
     </td>
     <td>
     </td>
     <td>Y
     </td>
     <td>Y
     </td>
     <td>0
     </td>
     <td>0
     </td>
     <td>0
     </td>
     <td>0
     </td>
     <td>0
     </td>
     <td>0
     </td>
     <td>1
     </td>
     <td>2
     </td>
    </tr>
    <tr>
     <td>ax-q-axgroup-001-consumer-group-001
     </td>
     <td>Y
     </td>
     <td>
     </td>
     <td>
     </td>
     <td>0
     </td>
     <td>0
     </td>
     <td>0
     </td>
     <td>0
     </td>
     <td>0
     </td>
     <td>0
     </td>
     <td>12
     </td>
     <td>2
     </td>
    </tr>
    <tr>
     <td>ax-q-axgroup-001-consumer-group-001-dl
     </td>
     <td>Y
     </td>
     <td>
     </td>
     <td>
     </td>
     <td>0
     </td>
     <td>0
     </td>
     <td>0
     </td>
     <td>0
     </td>
     <td>0
     </td>
     <td>0
     </td>
     <td>0
     </td>
     <td>2
     </td>
    </tr>
    <tr>
     <td>ax-q-axgroup001-consumer-group-001
     </td>
     <td>Y
     </td>
     <td>
     </td>
     <td>
     </td>
     <td>0
     </td>
     <td>241K
     </td>
     <td>241K
     </td>
     <td>0
     </td>
     <td>21.7g
     </td>
     <td>21.7g
     </td>
     <td>12
     </td>
     <td>2
     </td>
    </tr>
    <tr>
     <td>ax-q-axgroup001-consumer-group-001-dl
     </td>
     <td>Y
     </td>
     <td>
     </td>
     <td>
     </td>
     <td>323
     </td>
     <td>323
     </td>
     <td>0
     </td>
     <td>52.4m
     </td>
     <td>52.4m
     </td>
     <td>0
     </td>
     <td>0
     </td>
     <td>2
     </td>
    </tr>
  </table>

  Because there are no messages/traffic coming for the specific analytics group **axgroup-001**, you observe the error "The report timed out" in the Edge UI dashboard or in the analytics dashboards.

### Resolution

To resolve this issue, delete the `axgroup` that doesn't have any scopes and doesn't get any traffic.

Follow the procedure below to delete the `axgroup` :

**Step 1:  Delete the consumers for the particular axgroup.**

1. Use the following management API to remove each of consumers from the `axgroup`:

  ```
  curl -v -u admin@email.com:password -X DELETE -H 'Accept:application/json' -H 'Content-Type:application/json' 'http://{mgmt-server-host}:8080/v1/analytics/groups/ax/{axgroup-name}/consumer-groups/{consumer-group-name}/consumers/{uuid-of the consumer}'
  ```

2. Repeat the same API call above if there are multiple consumers, mentioning the UUID of each consumer in a separate API call.

  For the example shown above, the following API removes the consumer with UUID <code>5c1e9690-7b58-499a-a4bb-d54454474b8f:</code></strong>

  ```
  curl -v -X DELETE -H 'Accept:application/json' -H 'Content-Type:application/json' 'http://localhost:8080/v1/analytics/groups/ax/axgroup-001/consumer-groups/consumer-group-001/consumers/5c1e9690-7b58-499a-a4bb-d54454474b8f'
  
  {
    "name" : "axgroup-001",
    "properties" : {
    },
    "scopes" : [ ],
    "uuids" : {
      "qpid-server" : [ "5c1e9690-7b58-499a-a4bb-d54454474b8f", "7794c428-e553-4ed2-843d-69f93bbec8a3" ],
      "postgres-server" : [ "3b28b790-ec4e-45c5-a8d0-6d6f2088da65:750cd8ba-1799-4dfb-8c74-548010e95e5e" ]
    },
    "consumer-groups" : [ {
      "name" : "consumer-group-001",
      "consumers" : [ "7794c428-e553-4ed2-843d-69f93bbec8a3" ],
      "datastores" : [ "3b28b790-ec4e-45c5-a8d0-6d6f2088da65:750cd8ba-1799-4dfb-8c74-548010e95e5e" ],
      "properties" : {
      }
    } ],
    "data-processors" : {
    }
  * Connection #0 to host localhost left intact
  * Closing connection #0
  }
  ```

3. Re-run the same API to delete the other consumer whose UUID is **7794c428-e553-4ed2-843d-69f93bbec8a3** in the present example.

**Step 2 : Remove the consumer groups**

1. Use the following management API to remove the consumer groups from the particular `axgroup`:

  ```
  curl -v -u admin@email.com:password -X DELETE 'http://{mgmt-server-host}:8080/v1/analytics/groups/ax/{axgroup-name}/consumer-groups/{consumer-group-name}'
  ```

  **Example:**

  The following API deletes the consumer group name **consumer-group-001:**

  ```
  curl -v -X DELETE 'http://localhost:8080/v1/analytics/groups/ax/axgroup-001/consumer-groups/consumer-group-001'
  {
    "name" : "axgroup-001",
    "properties" : {
    },
    "scopes" : [ ],
    "uuids" : {
      "qpid-server" : [ "5c1e9690-7b58-499a-a4bb-d54454474b8f", "7794c428-e553-4ed2-843d-69f93bbec8a3" ],
      "postgres-server" : [ "3b28b790-ec4e-45c5-a8d0-6d6f2088da65:750cd8ba-1799-4dfb-8c74-548010e95e5e" ]
    },
    "consumer-groups" : [ {
      "name" : "consumer-group-001",
      "consumers" : [ ],
      "datastores" : [ "3b28b790-ec4e-45c5-a8d0-6d6f2088da65:750cd8ba-1799-4dfb-8c74-548010e95e5e" ],
      "properties" : {
      }
    } ],
    "data-processors" : {
    }
  * Connection #0 to host localhost left intact
  * Closing connection #0
  }
  ```

**Step 3: Delete the qpid-servers from axgroup**

1. Use the following management API to delete `qpid-servers` from the particular `axgroup`.

  ```
  curl -X DELETE -u admin@email.com "http://localhost:8080/v1/analytics/groups/ax/{axgroup-name}/servers?uuid={qpid-server-uuid}type=qpid-server" -H "Content-type: application/json"
  ```

2. Re-run the same API call if there are multiple Qpid servers.

  **Example:**

  Use the following API to delete the Qpid server with UUID  **7794c428-e553-4ed2-843d-69f93bbec8a3** in the present example:

  ```
  curl -X DELETE "http://localhost:8080/v1/analytics/groups/ax/axgroup-001/servers?uuid=7794c428-e553-4ed2-843d-69f93bbec8a3&type=qpid-server" -H "Content-type: application/json"

  {
    "name" : "axgroup-001",
    "properties" : {
    },
    "scopes" : [ ],
    "uuids" : {
      "qpid-server" : [ "5c1e9690-7b58-499a-a4bb-d54454474b8f" ],
      "postgres-server" : [ "3b28b790-ec4e-45c5-a8d0-6d6f2088da65:750cd8ba-1799-4dfb-8c74-548010e95e5e" ]
    },
    "consumer-groups" : [ {
      "name" : "consumer-group-001",
      "consumers" : [ ],
      "datastores" : [ "3b28b790-ec4e-45c5-a8d0-6d6f2088da65:750cd8ba-1799-4dfb-8c74-548010e95e5e" ],
      "properties" : {
      }
    } ],
    "data-processors" : {
    }
  }
  ```


**Step 4:  Delete the postgres servers from axgroup**


1.  Use the following management API to delete the Postgres server, if there is a single Postgres server:

    ```
    curl -v -X DELETE -H 'Accept:application/json'  "http://{mgmt-server-host}:8080/v1/analytics/groups/ax/{axgroup-name}/servers?uuid={postgres-server-uuid}&type=postgres-server&force=true"
    ```

1.  Use the following management API to delete the Postgres servers if you have master and Postgres slave setup

    ```
    curl -v -X DELETE -H 'Accept:application/json'  "http://{mgmt-server-host}:8080/v1/analytics/groups/ax/{axgroup-name}/servers?uuid={postgres-master-uuid,postgres-slave-uuid}&type=postgres-server&force=true"
    ```

  **Example:** 

  In the example shown above, there are master and slave Postgres servers, so you can use the following API to delete the Postgres servers:

  ```
  curl -v -X DELETE -H 'Accept:application/json'  "http://localhost:8080/v1/analytics/groups/ax/axgroup-001/servers?uuid=3b28b790-ec4e-45c5-a8d0-6d6f2088da65,750cd8ba-1799-4dfb-8c74-548010e95e5e&type=postgres-server&force=true"

  {
  "name" : "axgroup-001",
  "properties" : {
  },
  "scopes" : [ ],
  "uuids" : {
  "qpid-server" : [ ],
  "postgres-server" : [ ]
  },
  "consumer-groups" : [ ],
  "data-processors" : {
  }
  * Connection #0 to host localhost left intact
  * Closing connection #0
  }

  ```


**STEP 5: Remove the analytics group**

1. Use the following management API to remove the analytics group:

  ```
  curl -v -X DELETE "http://{mgmt-server-host}:8080/v1/analytics/groups/ax/{axgroup-name}"
  ```

  **Example:**

  ```
  curl -v -X DELETE "http://localhost:8080/v1/analytics/groups/ax/axgroup-001"
  {
    "properties" : {
    },
    "scopes" : [ ],
    "uuids" : {
    },
    "consumer-groups" : [ ],
    "data-processors" : {
    }
  * Connection #0 to host localhost left intact
  * Closing connection #0
  }
  ```


**STEP 6: Check if the group is completely removed**

1. Use the following management API to check if the particular analytics group has been completely removed:

  ```
  curl -v -u admin@email.com -X GET "http://{mgmt-server-host}:8080/v1/analytics/groups/ax 
  ```

  **Example:**


  ```
  curl localhost:8080/v1/analytics/groups/ax
  [ {
    "name" : "axgroup001",
    "properties" : {
      "consumer-type" : "ax"
    },
    "scopes" : [ "017pdspoint~dev", "010test~dev", "019charmo~dev", "009gcisearch1~dev", "000fj~trial-fjwan", "009dekura~dev", "008pisa~dev", "004fjadrms~dev", "018k5billing~dev", "004study14~dev", "001teama~dev", "005specdb~dev", "test~dev", "000fj~prod-fjwan", "012pjweb~dev", "020workflow~dev", "007ikou~prod-fjwan", "003asano~dev", "013mims~dev", "006studyhas~dev", "006efocus~dev", "002wfproto~dev", "016mediaapi~dev", "015skillnet~dev", "014aclmanager~dev", "010fjppei~dev", "000fj~trial", "003esupport~dev", "000fj~prod", "005ooi~dev", "test~elb1", "007fjauth~dev", "011osp~dev", "002study~dev" ],
    "uuids" : {
      "qpid-server" : [ "5c1e9690-7b58-499a-a4bb-d54454474b8f", "7794c428-e553-4ed2-843d-69f93bbec8a3" ],
      "aries-datastore" : [ ],
      "postgres-server" : [ "3b28b790-ec4e-45c5-a8d0-6d6f2088da65:750cd8ba-1799-4dfb-8c74-548010e95e5e" ],
      "dw-server" : [ ]
    },
    "consumer-groups" : [ {
      "name" : "consumer-group-001",
      "consumers" : [ "5c1e9690-7b58-499a-a4bb-d54454474b8f", "7794c428-e553-4ed2-843d-69f93bbec8a3" ],
      "datastores" : [ "3b28b790-ec4e-45c5-a8d0-6d6f2088da65:750cd8ba-1799-4dfb-8c74-548010e95e5e" ],
      "properties" : {
      }
    } ],
    "data-processors" : {
    }
  } ]
  ```

  Notice that there is no information pertaining to particular analytics group **axgroup-001** in the above output. This confirms that **axgroup-001** has been completely removed.

**Step 7: Restart processes**

The following processes on the Qpid and Postgres machines:

1. Restart the apigee-qpidd.
1. Restart edge-qpid-server.
1. Restart edge-postgres-server.
1. Restart apigee-postgresql.

**Step 8: Verify**

Verify if the data shows up in the analytics dashboards.

If the problem still persists, go to [Must Gather Diagnostic Information](/api-platform/troubleshoot/analytics/report-timed-out#must_gather_diagnostic_information).


## Must Gather Diagnostic Information

If the problem persists even after following the above instructions, please gather the following diagnostic information. Contact [Apigee Support](https://support.apigee.com/) and share the gathered information.


1.  Architecture setup of your Private Cloud install (how many hosts setup, number of each of the components).
1.  Output of the following commands:

    1.  Analytics Group

        ```
        curl -u sysadminEmail:sysadminPwd	http://{mgmt-server-host}:8080/v1/analytics/groups/ax
        ```

    1.  Qpid Queue stats on each of the Qpid machines

        ```
        qpid-stat -q
        ```

    1.  Analytics Status

        ```
        curl -u sysadminEmail:sysadminPwd http://{mgmt-server-host}:8080/v1/organizations/{org-name}/environments/{environment-name}/provisioning/axstatus

