{% extends "_base.html" %} {% block title %}PostgreSQL upgrade failure{% endblock %} {% block body %}

<aside class="note">Was this troubleshooting playbook helpful? Please let us know
  by clicking <a href="" class="google-feedback"
   data-p="1636213" data-b="docsite" data-context="troubleshooting-feedback">
  Send Feedback 
</a>.</aside>



## Symptom

You get an error when updating the PostgreSQL database.

## Error Message

```
ERROR: PostgreSQL Upgrade FAILED 
```

## Possible Causes

<table>
  <thead>
  <tr>
   <th>Cause
   </th>
   <th>Description
   </th>
   <th>Troubleshooting instructions apply to
   </th>
  </tr>
  </thead>
  <tbody>
  <tr>
   <td><a href="#cause_incorrect_replication_settings_in_postgresql_configuration_file">Incorrect replication settings in PostgreSQL configuration file</a>
   </td>
   <td>The PostgreSQL upgrade fails due to incorrect replication settings during the upgrade.
   </td>
   <td>Edge Private Cloud Users
   </td>
  </tr>
  <tr>
   <td><a href="#cause_postgresql_installation_performed_by_another_install_user_other_than_the_apigee_user">PostgreSQL installation performed by another install user other than the apigee user</a>
   </td>
   <td>PostgreSQL was installed initially with another user as an install user,
     which leads to upgrade failure.
   </td>
   <td>Edge Private Cloud Users
   </td>
  </tr>
  </tbody>
</table>


## Common Diagnosis Steps

If you get an error when upgrading the PostgreSQL database, perform the following
diagnostic steps first:


1.  Rename the PostgreSQL data folder:

  <pre class="devsite-terminal">
  mv /opt/apigee/data/apigee-postgresql/pgdata /opt/apigee/data/apigee-postgresql/pgdata-bkp
  </pre>

1.  Ensure that your original backup data is in a folder named <code>/opt/apigee/data/apigee-postgresql/pgdata-<var>version</var>.old/</code>

  For example:

  ```
  /opt/apigee/data/apigee-postgresql/pgdata-9.6.old/
  ```


1.  Restore the backup data in <code>/opt/apigee/data/apigee-postgresql/pgdata-<var>version</var>.old/</code> to `/opt/apigee/data/apigee-postgresql/pgdata` using following command:

  <pre class="devsite-terminal">
  mv /opt/apigee/data/apigee-postgresql/pgdata-<var>version</var>.old/ /opt/apigee/data/apigee-postgresql/pgdata 
  </pre>

  For example:

  <pre class="devsite-terminal">
  mv /opt/apigee/data/apigee-postgresql/pgdata-9.6.old/ /opt/apigee/data/apigee-postgresql/pgdata</pre>

1.  Rerun the `db_upgrade` command:	

  <pre class="devsite-terminal">/opt/apigee/apigee-service/bin/apigee-service apigee-postgresql db_upgrade
  </pre>
  
1.  If you get following error...

  ```
  pg_resetxlog: could not open file "global/pg_control" for reading:
  No such file or directory
  ```

  ...then rename the `pg_control.old` file to `pg_control` using following command:

  <pre class="devsite-terminal">
  mv /apigee/apigeeinst/data/apigee-postgresql/pgdata/global/pg_control.old /apigee/apigeeinst/data/apigee-postgresql/pgdata/global/pg_control 
  </pre>

1.  Rerun the `db_upgrade` command:	

  <pre class="devsite-terminal">
  /opt/apigee/apigee-service/bin/apigee-service apigee-postgresql db_upgrade
  </pre>

  If the problem persists, go to [Cause: Incorrect replication settings in PostgreSQL configuration file](#cause_incorrect_replication_settings_in_postgresql_configuration_file).


## Cause: Incorrect replication settings in PostgreSQL configuration file


### _Diagnosis_


1.  Check if the PostgreSQL configuration file `/opt/apigee/apigee-postgresql/conf/pg_hba.conf`
has the replication settings that you intend.
1.  If this file has the expected replication settings, then go to [PostgreSQL installation performed by another install user other than "apigee" user](#cause_postgresql_installation_performed_by_another_install_user_other_than_the_apigee_user).
1.  If not, go to [Resolution](#resolution).


### _Resolution_


1.  Rename the PostgreSQL data folder using the following command:

  <pre class="devsite-terminal">
  mv /opt/apigee/data/apigee-postgresql/pgdata /opt/apigee/data/apigee-postgresql/pgdata-bkp
  </pre>

1.  Ensure that the original backup data is in a folder named: 	<code>/opt/apigee/data/apigee-postgresql/pgdata-<var>version</var>.old/</code>

  For example:

  ```
  /opt/apigee/data/apigee-postgresql/pgdata-9.6.old/
  ```

1.  Restore the backup data from <code>/opt/apigee/data/apigee-postgresql/pgdata-<var>version</var>.old/</code> to `/opt/apigee/data/apigee-postgresql/pgdata` using following command:

  <pre class="devsite-terminal">
  mv /opt/apigee/data/apigee-postgresql/pgdata-<var>version</var>.old/ /opt/apigee/data/apigee-postgresql/pgdata 
  </pre>

  For example:

  <pre class="devsite-terminal">
  mv /opt/apigee/data/apigee-postgresql/pgdata-9.6.old/ /opt/apigee/data/apigee-postgresql/pgdata 
  </pre>

1.  If you ever changed slave host then you must update following property in
`/opt/apigee/customer/application/postgresql.properties` to update the config file
`pg_hba.conf`:

  ```
  conf_pg_hba_replication.connection=host replication apigee
  existing_slave_ip/32 trust\ \nhost replication apigee new_slave_ip/32 trust 
  ```

1.  Restart the `apigee-postgresql` service to ensure that the property set in
Step 4 above gets updated into the config file `pg_hba.conf`:

  <pre class="devsite-terminal">
  /opt/apigee/apigee-service/bin/apigee-service apigee-postgresql restart
  </pre>

1.  Make the config file `pg_hba.conf` immutable using the following command to
ensure that it does not get updated with incorrect settings during the PostgreSQL upgrade:

  <pre class="devsite-terminal">
  chattr +i /opt/apigee/apigee-postgresql/conf/pg_hba.conf 
  </pre>

1.  Rerun the `db_upgrade` command:

   <pre class="devsite-terminal">
   /opt/apigee/apigee-service/bin/apigee-service apigee-postgresql db_upgrade
   </pre>
   
1.  Remove the immutable setting on the config file `pg_hba.conf`:

  <pre class="devsite-terminal">
  chattr -i /opt/apigee/apigee-postgresql/conf/pg_hba.conf
  </pre>

## Cause: PostgreSQL installation performed by another install user other than the `apigee` user


### _Diagnosis_


1.  Check if there is any other user that has **rolesuper** set to **true** by
following these steps:

    1.  On the PostgreSQL node, log in to PostgreSQL using following command:

      <pre class="devsite-terminal">
      psql -h $(hostname -i)  -U apigee
      </pre>

    1.  Run the following SQL query:

      <pre>
      select * from pg_authid;
      </pre>

    1.  Check if there are multiple users having **rolesuper** set to **true**.
    If there are, then the PostgreSQL upgrade is failing because another user
    has been set as **install user** instead of the **apigee** user. Any user with the
    **rolesuper** role and a lower OID is considered to be an install user.

      To check this kind of role problem, run the following SQL queries:

        ```
        select oid,rolname,rolsuper from pg_roles;
        select * from pg_user;
        ```

### _Resolution_

1.  Login to PostgreSQL using following command:

  <pre class="devsite-terminal">
  psql -h $(hostname -i)  -U apigee
  </pre>

1.  Rename the existing `apigee` role in old DB to a temporary user (for example: `apigee2`):

    <pre class="devsite-terminal">
    update pg_authid set rolname ='apigee2' where rolname = 'apigee'; 
    </pre>

1.  Let's say there was another install user `srcapige`. Rename the `srcapige`
role to `apigee`:

    <pre class="devsite-terminal">
    update pg_authid set rolname ='apigee' where rolname = 'srcapigee';
    </pre>

1.  Rename the `apigee2` to the `srcapige` user:

    <pre class="devsite-terminal">
    update pg_authid set rolname ='secapigee' where rolname = 'apigee2'; 
    </pre>

1.  Update the password for all the renamed users:

  ```
  ALTER ROLE apigee WITH PASSWORD '<secret>'; 
  ALTER ROLE srcapige WITH PASSWORD '<secret>'; 
  ```

1.  Rename the PostgreSQL data folder using the following command:

    <pre class="devsite-terminal">
    mv /opt/apigee/data/apigee-postgresql/pgdata /opt/apigee/data/apigee-postgresql/pgdata-bkp
    </pre>

1.  Ensure that there is original backup data inside a folder named
<code>/opt/apigee/data/apigee-postgresql/pgdata-<var>version</var>.old/</code>

  For example:

  ```
  /opt/apigee/data/apigee-postgresql/pgdata-9.6.old/
  ```


1.  Restore the data in the <code>/opt/apigee/data/apigee-postgresql/pgdata-<var>version</var>.old/</code>
folder to `/opt/apigee/data/apigee-postgresql/pgdata` using following command:

  <pre class="devsite-terminal">
  mv /opt/apigee/data/apigee-postgresql/pgdata-<var>version</var>.old/ /opt/apigee/data/apigee-postgresql/pgdata 
  </pre>

  For example:

  <pre class="devsite-terminal">
  mv /opt/apigee/data/apigee-postgresql/pgdata-9.6.old/ /opt/apigee/data/apigee-postgresql/pgdata 
  </pre>

1.  Rerun the `db_upgrade` command:

  <pre class="devsite-terminal">
  /opt/apigee/apigee-service/bin/apigee-service apigee-postgresql db_upgrade 
  </pre>

1.  Restart all `apigee-qpidd` and `edge-qpid-server` services:

  <pre>
  <code  class="devsite-terminal">/opt/apigee/apigee-service/bin/apigee-service apigee-qpidd restart</code>
  <code  class="devsite-terminal">/opt/apigee/apigee-service/bin/apigee-service edge-qpid-server restart</code>
  </pre>

If the problem still persists, go to [Must gather diagnostic information](https://docs.google.com/document/d/1RsjffiHVenh83vFtfvjEHSBZ5_nNq12FIE4vnFdpX4Y/edit#heading=h.xmsd98bypv0k).		


## Must gather diagnostic information

If the problem persists after following the above troubleshooting instructions,
gather the following diagnostic information. Contact [Apigee Support](https://support.apigee.com/) and share this information with the support team:



1.  Add `set -x` on the second line of the`/opt/apigee/apigee-postgresql/lib/actions/db_upgrade` file.
1.  Follow the [Common diagnosis steps](#common_diagnosis_steps) and provide the
console output of the `db_upgrade` command to the support team:

  <pre class="devsite-terminal">
  opt/apigee/apigee-service/bin/apigee-service apigee-postgresql db_upgrade
  </pre>

1.  Provide the following log files to the support team:

  ```
  /opt/apigee/var/log/apigee-postgresql/apigee-postgresql.log
  /opt/apigee/var/log/apigee-postgresql/update.log
  ```

1.  Provide the output of the following operating system commands to check if
alternatives are set to `auto`.

  Note: The following commands are operating system specific, may differ depending
  on your operating system. Refer to your system's documentation for more information.

  <pre class="devsite-terminal">
  update-alternatives --display psql 
  </pre>
  
  OR 

  <pre>
  <code  class="devsite-terminal">update-alternatives --display pgsql</code>
  <code  class="devsite-terminal">update-alternatives --list psql</code>
  </pre>

  OR 

  <pre class="devsite-terminal">
  update-alternatives --list pgsql
  </pre>
  
  If alternatives are set to `manual`, you can set them to `auto` using following
  command:

  <pre class="devsite-terminal">
  update-alternatives --auto psql
  </pre>
  
  Setting the alternatives to `auto` points the `psql` and `postgres` binaries to
  the upgraded versions. Otherwise, the binaries will point to the older version,
  which can cause issues.
    
{% endblock %}
