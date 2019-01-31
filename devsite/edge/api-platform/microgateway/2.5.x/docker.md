{% extends "_base.html" %} {% block title %}Using Docker for Edge Microgateway{% endblock %} {% block body %}


This topic explains how to run Edge Microgateway in a Docker container. The steps
covered in this topic assume a basic understanding of Docker, Docker commands, and Edge Microgateway
setup and configuration. For
more information, refer to the documentation for [Docker](https://docs.docker.com/)
and [Edge Microgateway](/api-platform/microgateway/2.5.x/setting-and-configuring-edge-microgateway)

## Prerequisites

Before running Edge Microgateway in a Docker container, you must do the following
tasks:

* Configure Edge Microgateway for your Apigee organization/environment:

  <pre class="devsite-terminal">edgemicro configure -o <var>your_org</var> -e <var>your_env</var> -u <var>your_username</var></pre>

  For more details on configuration, see [Part 1: Configure Edge Microgateway](/api-platform/microgateway/2.5.x/setting-and-configuring-edge-microgateway#part1configureedgemicrogateway).

* After performing the configuration steps, locate the configuration
file. The default location is here:

  <pre>$HOME/.edgemicro/<var>your_org</var>-<var>your_env</var>-config.yaml</pre>
  
  where `your_org` and `your_env` are the organization
  and environment you used when you ran the `edgemicro config` command. You will need
  this file when you start Edge Microgateway in a Docker container.

* Be sure you have the key and secret credentials that were returned when you ran the
`edgemicro config` command. For example:

  ```
  The following credentials are required to start edge micro
    key: d9c34e1aff68ed969273c016699eabf48780e4f652242e72fc88a43e21252cb0
    secret: 3bc95a71c86a3c8ce04537fbcb788158731t51dfc6cdec13b7c05aa0bd969430
  ```
  
* Before you start Edge Microgateway in a Docker container, you need to create
(or have created) the Apigee Edge entities that are required
to make authenticated API proxy calls. These entities include an Edge Microgateway-aware
proxy, an API Product, a Developer, and a Developer App. For complete instructions, see
[Create entities on Apigee Edge](https://apigee.devsite.corp.google.com/api-platform/microgateway/2.5.x/setting-and-configuring-edge-microgateway#part2createentitiesonapigeeedge).

## Run Edge Micro as a Docker container

1. Download the Docker image for Edge Microgateway:

    <pre class="devsite-terminal">docker pull gcr.io/apigee-microgateway/edgemicro:latest</pre>
    
    <aside class="note">You can use a tag to specify a specific Edge Microgateway version.
  For example: <code>docker pull gcr.io/apigee-microgateway/edgemicro:2.5.26</code></aside>
  
1. Before you proceed to the next steps, be sure you have performed all of the steps in
the [Prerequisites]() section.

1. Run the following command to base64-encode the Edge Microgateway configuration file
located in `$HOME/.edgemicro`:

  <pre class="devsite-terminal">
  export EDGEMICRO_CONFIG=`base64 $HOME/.edgemicro/<var>your_org</var>-<var>your_env</var>-config.yaml`</pre>

  where `your_org` and `your_env` are the organization and environment you used when you
  ran the `edgemicro config` command.
  
  Remember to place back-ticks (\`) around the command. For example:
  
  <pre class="devsite-terminal">
  export EDGEMICRO_CONFIG=`base64 $HOME/.edgemicro/docs-test-config.yaml`</pre>
  
  <aside class="note">The version of Docker used to build the Edge Microgateway
    image accepts the configuration file
    as a base64-encoded string. Encoding allows you to pass the configuration file
    to Docker on the command line, as shown in the following step. If you're using
  Kubernetes, you can store the config file in a <a href="https://kubernetes.io/docs/concepts/configuration/secret/">Kubernetes Secrets</a> entity.</aside>

1. Run Edge Microgateway as a container. The command sets several environment variables
that are used by the container runtime to start Edge Microgateway:


  <pre class="devsite-terminal">docker run -P -p 8000:8000 -d --name edgemicro \
  -v /var/tmp:/opt/apigee/logs \
  -e EDGEMICRO_PROCESSES=1 \
  -e EDGEMICRO_ORG=<var>your_org</var> \
  -e EDGEMICRO_ENV=<var>your_env</var> \
  -e EDGEMICRO_KEY=<var>your_key</var> \
  -e EDGEMICRO_SECRET=<var>your_secret</var> \
  -e EDGEMICRO_CONFIG=$EDGEMICRO_CONFIG \
  -e SERVICE_NAME=edgemicro \
  --security-opt=no-new-privileges \
  --cap-drop=ALL \
  gcr.io/apigee-microgateway/edgemicro:latest</pre>

  **Parameters**
  
  <table>
    <thead>
       <th><strong>Parameter</strong>
       </th>
       <th><strong>Description</strong>
       </th>
    </thead>
    <tbody>
      <tr>
        <td><code>-P</code></td>
        <td>Publish all exposed ports to the host. See also <a href="https://docs.docker.com/v17.09/engine/reference/run/">Docker run reference</a>.</td>
      </tr>
       <tr>
        <td><code>-p</code></td>
        <td>Explicitly map a single port or range of ports. See also <a href="https://docs.docker.com/v17.09/engine/reference/run/">Docker run reference</a>.</td>
      </tr>
      <tr>
        <td><code>-d</code></td>
        <td>Run in detached mode. See also <a href="https://docs.docker.com/v17.09/engine/reference/run/">Docker run reference</a>.</td>
      </tr>
      <tr>
        <td><code>-v, --volume</code></td>
        <td>Specifies a volume mount. Note that if you configure Edge Microgateway
          to use TLS in the Docker container, you must expose port 8443 if you
          mount the log file directory. See also <a href="https://docs.docker.com/engine/reference/run/#volume-shared-filesystems">VOLUME [shared filesystems]</a>. See also <a href="#using_tls_in_the_docker_container">Using TLS in the Docker Container</a>.</td>
      </tr>
      <tr>
        <td><code>EDGEMICRO_ORG</code></td>
        <td>The name of the Apigee organization you used to configure Edge Microgateway.</td>
      </tr>
      <tr>
        <td><code>EDGEMICRO_ENV</code></td>
        <td>The name of the Apigee environment you used to configure Edge Microgateway.</td>
      </tr>

      <tr>
        <td><code>EDGEMICRO_PROCESSES</code></td>
        <td>The number of processes to start.</td>
      </tr>
      <tr>
        <td><code>EDGEMICRO_KEY</code></td>
        <td>The key returned when you configured Edge Microgateway.</td>
      </tr>

      <tr>
        <td><code>EDGEMICRO_SECRET</code></td>
        <td>The secret returned when you configured Edge Microgateway.</td>
      </tr>
      <tr>
        <td><code>EDGEMICRO_CONFIG</code></td>
        <td>A variable containing the base64-encoded Edge Microgateway configuration file.</td>
      </tr>

      <tr>
        <td><code>SERVICE_NAME</code></td>
        <td>If you are on Kubernetes, this parameter is auto-populated. Otherwise,
          you can set it to anything you wish. If you specify nothing, the service
          name is set to <code>default</code>.</td>
      </tr>
      <tr>
        <tr>
        <td><code>DEBUG</code></td>
        <td>Set to <code>*</code> to enable debugging. </td>
      </tr>
      <tr>
        <td><code>HTTP_PROXY</code>
            <code>HTTPS_PROXY</code></td>
        <td>Use when Edge Microgateway is
          running behind a firewall and the gateway cannot communicate with Apigee
          Edge. For more
          information, see <a href="/api-platform/microgateway/2.5.x/operation-and-configuration-reference-edge-microgateway#settingupedgemicrogatewaybehindacompanyfirewall">Setting up Edge Microgateway behind a company firewall</a>. 
          <p>For example: <code>HTTP_PROXY=http://10.203.0.1:5187/</code></p></td>
      </tr>
      <tr>
        <td><code>NO_PROXY</code></td>
        <td>A comma delimited list of domains that Edge Microgateway should not proxy to. 
          For more information, see <a href="/api-platform/microgateway/2.5.x/operation-and-configuration-reference-edge-microgateway#settingupedgemicrogatewaybehindacompanyfirewall">Setting up Edge Microgateway behind a company firewall</a>.
          <p>For example: <code>localhost,127.0.0.1,localaddress,.localdomain.com</code></p></td>
      </tr>
      <tr>
      <tr>
        <tr>
        <td><code>NODE_EXTRA_CA_CERTS</code></td>
        <td>(Optional) Use this parameter if you are using a CA that's not trusted
          by default by Node.js. Set the value of this parameter to the path to a
          file containing one or more trusted certificates in PEM format. For
          details, see <a href="#tls_certificates">TLS certificates</a>.</td>
      </tr>
      <tr>
        <td><code>--security-opt</code></td>
        <td>(Optional) Sets desired Docker security options. See <a href="https://docs.docker.com/engine/reference/run/#security-configuration">Security configuration</a> in the Docker documentation.</td>
      </tr>
      <tr>
        <td><code>--cap-drop</code></td>
        <td>(Optional) Sets limits on Linux capabilities permitted in the container. See <a href="https://docs.docker.com/engine/reference/run/#runtime-privilege-and-linux-capabilities">Runtime privilege and Linux capabilities</a> in the Docker documentation.</td>
      </tr>
    </tbody>
  </table>
  
  For example:
  
  <pre class="devsite-terminal">docker run -P -p 8000:8000 -d --name edgemicro \
  -v /var/tmp:/opt/apigee/logs \
  -e EDGEMICRO_PROCESS=1 \
  -e EDGEMICRO_ORG=docs \
  -e EDGEMICRO_ENV=test \
  -e EDGEMICRO_KEY=d9c34e1aff68ed969273b016699eabf48780e4f652242e72fc88a23e21252cb0 \
  -e EDGEMICRO_SECRET=3bc95a71c86a3c8ce04137fbcb788158731t51dfc6cdec13b7c05aa0bd969430 \
  -e EDGEMICRO_CONFIG=$EDGEMICRO_CONFIG \
  -e SERVICE_NAME=edgemicro \
  --security-opt=no-new-privileges \
  --cap-drop=ALL \
  gcr.io/apigee-microgateway/edgemicro</pre>
  
1. To check that the container is running:

  <pre class="devsite-terminal">docker ps</pre>
  
  You should see output similar to the following:
  
  ```
  CONTAINER ID    IMAGE         COMMAND                CREATED          STATUS          PORTS                                             NAMES
  8b92e082ea9c    edgemicro    "/tmp/entrypoint.sh"   12 minutes ago   Up 12 minutes    0.0.0.0:8000->8000/tcp, 0.0.0.0:32775->8443/tcp    edgemicro
  ```

## Testing an API call

After you start Edge Microgateway in the container, you can make API calls to it.
For example, if the basepath of your API is `/hello`:

<pre class="devsite-terminal">http://localhost:8000/hello</pre>

Sample output:

```
{"error":"missing_authorization","error_description":"Missing Authorization header"}
```

If you see this response, it means that Edge Microgateway successfully handled
the API call. However, by default, Edge Microgateway requires an API key for authentication.
In the next section, you will test the API with a valid API key.

## Test an API with a valid API key

In the Edge UI, navigate to the Developer App you created previously. In the Developer
App page, show the Consumer Key and copy it. This value is the API key. You'll use
this key to make authenticated API calls.

Call the API with the `x-api-key` header as follows. The Consumer Key value you
copied from the Developer App is the API key. By default, Edge Microgateway
expects you to pass the key in a header called `x-api-key`, like this:

<pre class="devsite-terminal">curl -i http://localhost:8000/hello -H "x-api-key:<var>apikey</var>"</pre>

For example:

<pre class="devsite-terminal">curl -i http://localhost:8000/hello -H "x-api-key:PydUKRDGIXRqF2xh4usn1FLHbhGKVIz"</pre>

If you want to learn more about making authenticated API calls through Edge Microgateway
with API keys and OAuth tokens, see [Part 4: Secure Edge Microgateway](https://apigee.devsite.corp.google.com/api-platform/microgateway/2.5.x/setting-and-configuring-edge-microgateway#part4secureedgemicrogateway).


## Stopping Edge Microgateway

Use the following Docker command to stop Edge Microgateway:

```
docker stop edgemicro
```

## Restarting Edge Microgateway

After stopping Edge Microgateway, you can restart it with this Docker command:

```
docker start edgemicro
```

## Using TLS in the Docker container

This section explains how to configure TLS for Edge Microgateway running
in a Docker container. You can configure the Edge Microgateway server to use TLS
for incoming requests (northbound direction), and you can configure Edge
Microgateway to be a TLS client for outgoing requests to target endpoints
(southbound direction).

### Where to put certificate files

The Docker container running Edge Microgateway has a mount point on `/opt/apigee/.edgemicro`.
When you configure Edge Microgateway to use TLS certificates, you can make the
certificate files available on that mount point and refer to them in the
Edge Microgateway configuration file. This config file is usually located in the `$HOME/.edgemicro`
directory, and is named <code><var>your_org</var>-<var>your_env</var>-config.yaml</code>.
For example:

```
...
edgemicro:
  ssl:
   key: /opt/apigee/.edgemicro/southbound/tls.key
   cert: /opt/apigee/.edgemicro/southbound/tls.crt
...
```


### Using a CA that is not trusted by Node.js

If you are using a Certificate Authority (CA) that's not trusted by default by
Node.js (such as is the case with a self-signed certificate), consider using
the parameter <code>NODE_EXTRA_CA_CERTS</code> when you run the container. 

Set this parameter to the path to a file containing one or more
trusted certificates in PEM format. To see how this parameter is used, see the
examples [How to configure northbound TLS](#example_how_to_configure_northbound_tls) and [How to configure southbound TLS](#example_how_to_configure_southbound_tls).

For example:

```
docker run -P -p 8443:8443 -d --name edgemicro \
-v $HOME/.edgemicro:/opt/apigee/.edgemicro \
-v $HOME/.edgemicro:/opt/apigee/logs \
-e NODE_EXTRA_CA_CERTS=/opt/apigee/.edgemicro/rootca.pem \
-e EDGEMICRO_PORT=8443 \
-e EDGEMICRO_ORG=docs \
-e EDGEMICRO_ENV=test \
-e EDGEMICRO_KEY=ac36574905fb54fdae65fc5433e831bec2680efb98220a355f2e917e52973c \
-e EDGEMICRO_SECRET=aac81dff6c326eaa222d53c15c8841fa78ea863bf4472568c9ce2d80a3bc56 \
-e EDGEMICRO_CONFIG=$EDGEMICRO_CONFIG \
--link helloworld:helloworld gcr.io/apigee-microgateway/edgemicro
```

See also [Run Edge Micro as a Docker container](#run_edge_micro_as_a_docker_container). 

### Turning off TLS validation

Warning: Turning off TLS validation is not recommended.

Although not recommended, in some cases you may wish to disable TLS validation
for Edge Microgateway running in a container. The capability for disabling TLS
is not built into the Edge Microgateway Docker container by default. To accomplish
the task, you must create a customized Docker image for Edge Microgateway. Below
are general instructions for building the custom image and turning off TLS validation.


1. Clone or download the Edge Microgateway source repository from
`https://github.com/apigee-internal/microgateway`.

1. `cd` to the `microgateway/kubernetes/docker/edgemicro` directory in the source code directory.

  For example:

  ```
  cd $HOME/git/microgateway/kubernetes/docker/edgemicro
  ```

1. Open the file `entrypoint.sh` and modify the code to accept the <code>NODE_TLS_REJECT_UNAUTHORIZED</code>
environment variable. Later, when you run the container, you'll specify a value for this
variable. 

1. Build the Docker container:

  ```
  docker build -t edgemicro .
  ```
1. When you run the container, specify specify the option <code>-e NODE_TLS_REJECT_UNAUTHORIZED = 1</code>.
For example:

```
docker run -P -p 8443:8443 -d --name edgemicro \
-v $HOME/.edgemicro:/opt/apigee/.edgemicro \
-v $HOME/.edgemicro:/opt/apigee/logs \
-e NODE_TLS_REJECT_UNAUTHORIZED = 1 \
-e EDGEMICRO_PORT=8443 \
-e EDGEMICRO_ORG=docs \
-e EDGEMICRO_ENV=test \
-e EDGEMICRO_KEY=ac36574905fb54fdae65fc5433e831bec2680efb98220a355f2e917e52973c \
-e EDGEMICRO_SECRET=aac81dff6c326eaa222d53c15c8841fa78ea863bf4472568c9ce2d80a3bc56 \
-e EDGEMICRO_CONFIG=$EDGEMICRO_CONFIG \
--link helloworld:helloworld gcr.io/apigee-microgateway/edgemicro
```

### Example: How to configure northbound TLS

This section explains how to set up a northbound (incoming) TLS connection on the Edge Microgateway
server. Northbound TLS allows clients to use HTTPS when making API calls to Edge Microgateway.
The example below uses self-signed certificates.

Note: The following procedure is an example only. It is shown to give you an idea
of how to accomplish northbound TLS with Edge Microgateway. Your own configuration
may vary from the example.


#### 1. Intital setup steps
 
1. Locate the `openssl.cnf` file on your system. For example, `/etc/ssl/openssl.cnf`.
2. Open the `opensssl.cnf` file for editing.
3. Be sure the `req_extensions` are present in your config file. For example, you
should have information similar to the following in your file:

  ```
  [ req ]
  ...
  req_extensions          = v3_req
  ...

  [ v3_req ]
  extendedKeyUsage = serverAuth, clientAuth, codeSigning, emailProtection
  basicConstraints = CA:FALSE
  keyUsage = nonRepudiation, digitalSignature, keyEncipherment
  ```
  
3. Add the following stanza to `openssl.cnf` to generate the right SNI attributes:

  ```
  [ alt_names ]
  DNS.1 = www.example.com
  DNS.2 = example.com
  DNS.3 = localhost
  DNS.4 = localhost.localdomain
  DNS.5 = 127.0.0.1
  DNS.6 = ::1
  DNS.7 = fe80::1
  ```
  
  Example `opensssl.cnf` file:
  
  ```
  [ req ]
  distinguished_name      = req_distinguished_name
  attributes              = req_attributes
  req_extensions          = v3_req

  [ v3_req ]
  extendedKeyUsage = serverAuth, clientAuth, codeSigning, emailProtection
  basicConstraints = CA:FALSE
  keyUsage = nonRepudiation, digitalSignature, keyEncipherment

  [ req_distinguished_name ]
  countryName                     = Country Name (2 letter code)
  countryName_min                 = 2
  countryName_max                 = 2
  stateOrProvinceName             = State or Province Name (full name)
  localityName                    = Locality Name (eg, city)
  0.organizationName              = Organization Name (eg, company)
  organizationalUnitName          = Organizational Unit Name (eg, section)
  commonName                      = Common Name (eg, fully qualified host name)
  commonName_max                  = 64
  emailAddress                    = Email Address
  emailAddress_max                = 64

  [ req_attributes ]
  challengePassword               = A challenge password
  challengePassword_min           = 4
  challengePassword_max           = 20
  
  [ alt_names ]
  DNS.1 = www.example.com
  DNS.2 = example.com
  DNS.3 = localhost
  DNS.4 = localhost.localdomain
  DNS.5 = 127.0.0.1
  DNS.6 = ::1
  DNS.7 = fe80::1
  ```
1. Follow the steps given in the [Prerequisites](#prerequisites) section to initialize and
configure Edge Microgateway, if you haven't already done so. When completed, you
should have created an Edge Microgateway-aware proxy, an API Product, a Developer,
and a Developer App. In addition, you should have run the `edgemicro configure` command
and received a key and secret.
  
#### 2. Generate self-signed certificates

Next, generate the certificates and keys that you'll need to establish TLS:

1. `cd` to the `$HOME/.edgemicro` directory.
1. Create the following bash script. You can name it anything you wish. For example:
`keygen.sh`.

  <pre>
  #!/bin/bash
  # generate ca
  openssl genrsa -out rootca.key 2048
  openssl req -x509 -new -nodes -key rootca.key -sha256 -days 1024 -out rootca.pem
  # generate key
  openssl genrsa -out tls.key 2048
  openssl req -new -key tls.key -out tls.csr
  # sign cert
  openssl x509 -req -in tls.csr -CA rootca.pem -CAkey rootca.key -CAcreateserial -out tls.crt -days 1024 -sha256 -extensions 'v3_req' -extfile <var>path</var>/openssl.cnf
  </pre>
    
1. In the bash file, make sure the path to the `openssl.cnf` file is correct.

1. Execute the bash file. You will be prompted for certificate information. Be sure to use
`localhost` for the Common Name.

1. Check that the following files were created:

  * `rootca.key`
  * `rootca.pem`
  * `tls.key`
  * `tls.csr`
  * `rootca.srl`
  * `tls.crt`

#### 3. Edit the Edge Microgateway config file

1. Open the Edge Micro config file in an editor. For example:

  ```
  vi $HOME/.edgemicro/myorg-test-config.yaml
  ```

1. Edit the `edgemicro` stanza as follows. Note that you are making changes
to the `port` and `ssl` attributes:

  ```
  edge_config:
  ...
  edgemicro:
    port: 8443
    max_connections: 1000
    config_change_poll_interval: 600
    ssl:
      key: /opt/apigee/.edgemicro/tls.key
      cert: /opt/apigee/.edgemicro/tls.crt
      passphrase: admin123
      rejectUnauthorized: true
      requestCert: false
    logging:
  ...
  ```
  
1. Execute the following command to base64-encode the Edge Microgateway configuration file
located in `$HOME/.edgemicro`:

  <pre class="devsite-terminal">
  export EDGEMICRO_CONFIG=`base64 $HOME/.edgemicro/<var>your_org</var>-<var>your_env</var>-config.yaml`</pre>

  where `your_org` and `your_env` are the organization and environment you used when you
  ran the `edgemicro config` command.
  
  Remember to place back-ticks (\`) around the command. For example:
  
  <pre class="devsite-terminal">
  export EDGEMICRO_CONFIG=`base64 $HOME/.edgemicro/docs-test-config.yaml`</pre>

See also, [Run Edge Micro as a Docker container](#run_edge_micro_as_a_docker_container).

#### 4. Run the container

1. Execute the following command to run the Docker container with Edge Microgateway:


  Note: For details on the individual command parameters, see Step 4 in [Run Edge Micro as a Docker container](#run_edge_micro_as_a_docker_container).

  <pre>
  docker run -P -p 8443:8443 -d --name edgemicro \
  -v <var>path_to_your_edgemicro_dir</var>:/opt/apigee/.edgemicro \
  -v <var>path_to_your_logs_dir</var>:/opt/apigee/logs \
  -e NODE_EXTRA_CA_CERTS=/opt/apigee/.edgemicro/rootca.pem \
  -e EDGEMICRO_PORT=8443 \
  -e EDGEMICRO_ORG=$EDGEMICRO_ORG \
  -e EDGEMICRO_ENV=$EDGEMICRO_ENV \
  -e EDGEMICRO_KEY=$EDGEMICRO_KEY \
  -e EDGEMICRO_SECRET=$EDGEMICRO_SECRET \
  -e EDGEMICRO_CONFIG=$EDGEMICRO_CONFIG \
  gcr.io/apigee-microgateway/edgemicro:latest
  </pre>

1. Note the following parameters used in the command; they differ from the basic
command described in [Run Edge Micro as a Docker container](#run_edge_micro_as_a_docker_container).

  * The `port` is set to `8443`.
  * A volume mount is used to mount the key and cert files.
  * The `NODE_EXTRA_CA_CERTS` variable is used to add a custom CA (as needed in the case
  of self-signed certs).

#### 5. Test the TLS configuration

1. Execute the following cURL command to test the setup. Substitute
your basepath and API key into the command. The following example assumes you are in the directory where
`rootca.pem` is located and that the proxy you created has the basepath `/hello`:
 
  ```
  curl -v https://localhost:8443/hello --cacert rootca.pem \
  -H "x-api-key: Az82fdnfONVCOOE4NKhajxAboDgA3FAo"
  ```

2. The verbose cURL output shows each step of the
TLS handshaking. If you see an HTTP 200 response, the configuration succeeded:

  ```
  *   Trying ::1...ey:Az82fdnfONVCOOE4NKhajxAboDgA3FAo"
  * TCP_NODELAY set
  * Connected to localhost (::1) port 8443 (#0)
  * ALPN, offering h2
  * ALPN, offering http/1.1
  * Cipher selection: ALL:!EXPORT:!EXPORT40:!EXPORT56:!aNULL:!LOW:!RC4:@STRENGTH
  * successfully set certificate verify locations:
  *   CAfile: rootca.pem
    CApath: none
  * TLSv1.2 (OUT), TLS handshake, Client hello (1):
  * TLSv1.2 (IN), TLS handshake, Server hello (2):
  * TLSv1.2 (IN), TLS handshake, Certificate (11):
  * TLSv1.2 (IN), TLS handshake, Server key exchange (12):
  * TLSv1.2 (IN), TLS handshake, Server finished (14):
  * TLSv1.2 (OUT), TLS handshake, Client key exchange (16):
  * TLSv1.2 (OUT), TLS change cipher, Client hello (1):
  * TLSv1.2 (OUT), TLS handshake, Finished (20):
  * TLSv1.2 (IN), TLS change cipher, Client hello (1):
  * TLSv1.2 (IN), TLS handshake, Finished (20):
  * SSL connection using TLSv1.2 / ECDHE-RSA-AES128-GCM-SHA256
  * ALPN, server accepted to use http/1.1
  * Server certificate:
  *  subject: C=US; ST=CO; L=Boulder; O=Docs; OU=Docs; CN=localhost; emailAddress=docs@apigee.com
  *  start date: Dec 14 22:35:28 2018 GMT
  *  expire date: Oct  3 22:35:28 2021 GMT
  *  common name: localhost (matched)
  *  issuer: C=US; ST=CO; L=Boulder; O=Docs; OU=Docs; CN=localhost; emailAddress=docs@apigee.com
  *  SSL certificate verify ok.
  > GET /hello HTTP/1.1
  > Host: localhost:8443
  > User-Agent: curl/7.54.0
  > Accept: */*
  > x-api-key:Az82fdnfaONVCOE4NKhajxAboDA3FAo
  >
  < HTTP/1.1 200 OK
  < x-powered-by: Apigee
  < access-control-allow-origin: *
  < x-frame-options: ALLOW-FROM RESOURCE-URL
  < x-xss-protection: 1
  < x-content-type-options: nosniff
  < content-type: text/plain; charset=utf-8
  < etag: W/"d-GHB1ZrJKk/wdVTdB/jgBsw"
  < date: Fri, 14 Dec 2018 22:43:13 GMT
  < via: 1.1 google
  < alt-svc: clear
  < x-response-time: 1325
  < Connection: keep-alive
  < Transfer-Encoding: chunked
  <
  * Connection #0 to host localhost left intact
  Hello, Guest!
  ```







### Example: How to configure southbound TLS

This section explains how to set up a southbound (outgoing) TLS connection between
the Edge Microgateway server and a backend target application. The example below uses
self-signed certificates.

Note: The following procedure is an example only. It is shown to give you an idea
of how to accomplish a southbound TLS connection. Southbound TLS protects traffic
between the Edge Microgateway server and the target application. Your own
configuration may vary from the example.


#### 1. Initial setup steps

1. Locate the `openssl.cnf` file on your system. For example, `/etc/ssl/openssl.cnf`.
2. Open the `opensssl.cnf` file for editing.
3. Be sure the `req_extensions` are present in your config file. For example, you
should have information similar to the following in your file:

  ```
  [ req ]
  ...
  req_extensions          = v3_req
  ...

  [ v3_req ]
  extendedKeyUsage = serverAuth, clientAuth, codeSigning, emailProtection
  basicConstraints = CA:FALSE
  keyUsage = nonRepudiation, digitalSignature, keyEncipherment
  ```
  
3. Add the following stanza to `openssl.cnf` to generate the right SNI attributes:

  ```
  [ alt_names ]
  DNS.1 = helloworld
  DNS.2 = localhost
  DNS.3 = localhost.localdomain
  DNS.4 = 127.0.0.1
  DNS.5 = ::1
  DNS.6 = fe80::1
  ```
  
  Example `opensssl.cnf` file:
  
  ```
  [ req ]
  distinguished_name      = req_distinguished_name
  attributes              = req_attributes
  req_extensions          = v3_req

  [ v3_req ]
  extendedKeyUsage = serverAuth, clientAuth, codeSigning, emailProtection
  basicConstraints = CA:FALSE
  keyUsage = nonRepudiation, digitalSignature, keyEncipherment

  [ req_distinguished_name ]
  countryName                     = Country Name (2 letter code)
  countryName_min                 = 2
  countryName_max                 = 2
  stateOrProvinceName             = State or Province Name (full name)
  localityName                    = Locality Name (eg, city)
  0.organizationName              = Organization Name (eg, company)
  organizationalUnitName          = Organizational Unit Name (eg, section)
  commonName                      = Common Name (eg, fully qualified host name)
  commonName_max                  = 64
  emailAddress                    = Email Address
  emailAddress_max                = 64

  [ req_attributes ]
  challengePassword               = A challenge password
  challengePassword_min           = 4
  challengePassword_max           = 20
  
  [ alt_names ]
  DNS.1 = helloworld
  DNS.2 = localhost
  DNS.3 = localhost.localdomain
  DNS.4 = 127.0.0.1
  DNS.5 = ::1
  DNS.6 = fe80::1
  ```

1. Run the `edgemicro configure` command:

  <pre class="devsite-terminal">edgemicro configure -o <var>your_org</var> -e <var>your_env</var> -u <var>your_username</var></pre>

  For more details on configuration, see [Part 1: Configure Edge Microgateway](/api-platform/microgateway/2.5.x/setting-and-configuring-edge-microgateway#part1configureedgemicrogateway).
  
1. Copy the key and secret credentials that were returned from
`edgemicro configure`. You will need these values to run the container. For example:

  ```
  The following credentials are required to start edge micro
    key: d9c34e1aff68ed969273c016699eabf48780e4f652242e72fc88a43e21252cb0
    secret: 3bc95a71c86a3c8ce04537fbcb788158731t51dfc6cdec13b7c05aa0bd969430
  ```

#### 2. Create a Node.js target application

1. `cd` to the `.edgemicro` directory. 

  
1. Create the following bash script. You can name it anything you wish. For example:
  `keygen.sh`.

  <pre>
  #!/bin/bash
  # generate ca
  openssl genrsa -out rootca.key 2048
  openssl req -x509 -new -nodes -key rootca.key -sha256 -days 1024 -out rootca.pem
  # generate key
  openssl genrsa -out tls.key 2048
  openssl req -new -key tls.key -out tls.csr
  # sign cert
  openssl x509 -req -in tls.csr -CA rootca.pem -CAkey rootca.key -CAcreateserial -out tls.crt -days 1024 -sha256 -extensions 'v3_req' -extfile <var>path</var>/openssl.cnf
  </pre>
    
1. In the bash file, make sure the path to the `openssl.cnf` file is correct.

1. Execute the bash file. You will be prompted for certificate information. Be sure to use
`hellworld` for the Common Name.

1. Check that the following files were created:

  * `rootca.key`
  * `rootca.pem`
  * `tls.key`
  * `tls.csr`
  * `rootca.srl`
  * `tls.crt`

1. Create a new file called `server.js`.
  
  ```
  'use strict';

  const express = require('express');
  const https = require('https');
  const fs = require('fs');


  const options = {
    key: fs.readFileSync("tls.key"),
    cert: fs.readFileSync("tls.crt")
  };

  // Constants
  const PORT = 9443;
  const HOST = '0.0.0.0';

  // App
  const app = express();
  app.get('/', (req, res) => {
    res.send('Hello world\n');
  });

  https.createServer(options, app).listen(PORT);
  ```

1. Create a `package.json` file in the same directory as `server.js`. For example:

  ```
  {
    "name": "helloworld",
    "version": "1.0.0",
    "description": "",
    "main": "server.js",
    "scripts": {
      "test": "echo \"Error: no test specified\" && exit 1",
      "start": "node server.js"
    },
    "author": "",
    "license": "ISC",
    "dependencies": {
      "express": "^4.16.4",
      "fs": "0.0.1-security",
      "https": "^1.0.0"
    }
  }
  ```

1. Run `npm install` to get the dependencies.

1. Create a new Dockerfile in the same directory as `server.js`, where `WORKDIR`
is the path to the root of your Node.js app:

  <pre>
  FROM node:8-alpine
  WORKDIR <var>path-to-your-node-app</var>
  COPY package*.json ./

  RUN npm install
  COPY . .
  EXPOSE 9443
  CMD [ "npm", "start" ]
  </pre>

1. Build the Docker image:

  ```
  docker build -t helloworld . 
  ```

1. Start the sample app:

  ```
  docker run -P -p 9443:9443 --name helloworld helloworld
  ```

#### 3. Create entities on Apigee Edge

1. Create an Edge Microgateway-aware proxy with these settings. For more information,
see [Create an Edge Microgateway-aware API proxy on Edge](/api-platform/microgateway/2.5.x/setting-and-configuring-edge-microgateway#part2createentitiesonapigeeedge-1createanedgemicrogatewayawareapiproxyonedge).

  * Proxy name: `edgemicro_local`
  * Revision: `1`
  * Basepath: `/local`
  * Target: `https://helloworld:9443`

2. Create an API product. For details, see [Create a product](/api-platform/microgateway/2.5.x/setting-and-configuring-edge-microgateway#part2createentitiesonapigeeedge-2createaproduct).

3. Create a developer. For details, see [Create a developer](/api-platform/microgateway/2.5.x/setting-and-configuring-edge-microgateway#part2createentitiesonapigeeedge-3optionalcreateatestdeveloper).

4. Create a Developer app. For details, see [Create a developer app](/api-platform/microgateway/2.5.x/setting-and-configuring-edge-microgateway#part2createentitiesonapigeeedge-4createadeveloperapp)


#### 4. Run the container

1. Open the Edge Micro config file in an editor. For example:

  ```
  vi $HOME/.edgemicro/myorg-test-config.yaml
  ```

1. Edit the `edgemicro` stanza as follows. Note that you are making changes
to the `port` and `ssl` attributes:

  ```
  edge_config:
  ...
  edgemicro:
    port: 8443
    max_connections: 1000
    config_change_poll_interval: 600
    ssl:
      key: /opt/apigee/.edgemicro/tls.key
      cert: /opt/apigee/.edgemicro/tls.crt
      passphrase: admin123
      rejectUnauthorized: true
      requestCert: false
    logging:
  ...
  ```

1. Execute the following command to base64-encode the Edge Microgateway configuration file located in `$HOME/.edgemicro`:

  <pre class="devsite-terminal">
  export EDGEMICRO_CONFIG=`base64 $HOME/.edgemicro/<var>your_org</var>-<var>your_env</var>-config.yaml`</pre>

  where `your_org` and `your_env` are the organization and environment you used when you
  ran the `edgemicro config` command.
  
  Remember to place back-ticks (\`) around the command. For example:
  
  <pre class="devsite-terminal">
  export EDGEMICRO_CONFIG=`base64 $HOME/.edgemicro/docs-test-config.yaml`</pre>

1. Run the following command to start Edge Microgateway in the Docker container.

  Note: For details on the individual command parameters, see Step 4 in [Run Edge Micro as a Docker container](#run_edge_micro_as_a_docker_container).


  <pre>
  docker run -P -p 8443:8443 -d --name edgemicro \
  -v <var>path_to_your_edgemicro_dir</var>:/opt/apigee/.edgemicro \
  -v <var>path_to_your_logs_dir</var>:/opt/apigee/logs \
  -e EDGEMICRO_PORT=8443 \
  -e EDGEMICRO_ORG=$EDGEMICRO_ORG \
  -e EDGEMICRO_ENV=$EDGEMICRO_ENV \
  -e EDGEMICRO_KEY=$EDGEMICRO_KEY \
  -e EDGEMICRO_SECRET=$EDGEMICRO_SECRET \
  -e EDGEMICRO_CONFIG=$EDGEMICRO_CONFIG \
  --link helloworld:helloworld gcr.io/apigee-microgateway/edgemicro
  </pre>

  Note: The parameter `--link` is used to link the two containers.

#### 5. Test the TLS configuration

1. Execute the following cURL command to test the setup. Substitute
your the basepath you  used in the microgateway-aware proxy and the
API key obtained from the Developer app you created on Apigee Edge. For example:
 
  ```
  curl https://localhost:8443/local -k -H "x-api-key: xxxx" -v
  ```

  You should see this error:

  ```
  ...
  *  subject: C=CA; ST=Ontario; L=Toronto; O=Google Canada; OU=Google Cloud Platform; CN=edgemicro; emailAddress=srinandans@google.com
  *  start date: Dec 10 02:12:22 2018 GMT
  *  expire date: Sep 29 02:12:22 2021 GMT
  *  issuer: C=CA; ST=Ontario; L=Toronto; O=Google Canada; OU=Google Cloud Platform; CN=edgemicro; emailAddress=srinandans@google.com
  *  SSL certificate verify result: unable to get local issuer certificate (20), continuing anyway.
  > GET /local HTTP/1.1
  > Host: localhost:8443
  > User-Agent: curl/7.54.0
  > Accept: */*
  > x-api-key: 9fVC65pFj8LrmlPmVyxFjx4KgAHTxqSd
  >
  < HTTP/1.1 502 Bad Gateway
  < Date: Wed, 12 Dec 2018 05:25:01 GMT
  < Connection: keep-alive
  < Content-Length: 93
  <
  * Connection #0 to host localhost left intact
  {"message":"unable to verify the first certificate","code":"UNABLE_TO_VERIFY_LEAF_SIGNATURE"}
  ```

1. Re-run Edge Microgateway, but this time add the `NODE_EXTRA_CA_CERTS` variable.

  Note: The PEM file in the `NODE_EXTRA_CA_CERTS` variable must have the target's
  CA (in this case `helloworld`):

  <pre>
  docker run -P -p 8443:8443 -d --name edgemicro \
  -v <var>path_to_your_edgemicro_dir</var>:/opt/apigee/.edgemicro \
  -v <var>path_to_your_logs_dir</var>:/opt/apigee/logs \
  -e NODE_EXTRA_CA_CERTS=/opt/apigee/.edgemicro/rootca.pem \
  -e EDGEMICRO_PORT=8443 \
  -e EDGEMICRO_ORG=$EDGEMICRO_ORG \
  -e EDGEMICRO_ENV=$EDGEMICRO_ENV \
  -e EDGEMICRO_KEY=$EDGEMICRO_KEY \
  -e EDGEMICRO_SECRET=$EDGEMICRO_SECRET \
  -e EDGEMICRO_CONFIG=$EDGEMICRO_CONFIG \
  --link helloworld:helloworld gcr.io/apigee-microgateway/edgemicro
  </pre>

1. Execute the following cURL command. Substitute
your basepath and API key as before. For example:
 
  ```
  curl https://localhost:8443/local -k -H "x-api-key: xxxx" -v
  ```
  
1. Check the output. On success, you will get an HTTP 200 status response:

  ```
  ...
  > GET /local HTTP/1.1
  > Host: localhost:8443
  > User-Agent: curl/7.54.0
  > Accept: */*
  > x-api-key: 9fVC65pFj8LrmlPmVyxFjx4KgAHTxqSd
  >
  < HTTP/1.1 200 OK
  < x-powered-by: Express
  < content-type: text/html; charset=utf-8
  < etag: W/"c-M6tWOb/Y57lesdjQuHeB1P/qTV0"
  < date: Wed, 12 Dec 2018 05:49:28 GMT
  < x-response-time: 421
  < Connection: keep-alive
  < Transfer-Encoding: chunked
  <
  Hello world
  ```



## Adding a custom plugin

You can add new features and capabilities to the microgateway by writing [custom
plugins](/api-platform/microgateway/2.5.x/develop-custom-plugins). Custom plugins let you interact programmatically with the requests
and responses that flow through the microgateway.

You have two options for deploying plugins to an Edge Microgateway
instance running in a docker container:

* [Option A: Mount the plugins directory on a volume](#option_a_mount_the_plugins_directory_on_a_volume)
* [Option B: Build the plugins into the container](#option_b_build_the_plugins_into_the_container)

The rest of this section assumes
that you are familiar with writing and configuring
plugins for a standard Edge Microgateway setup. If not, see [Develop custom plugins](/api-platform/microgateway/2.5.x/develop-custom-plugins).


### Option A: Mount the plugins directory on a volume

(Added in v.2.5.27) The steps for adding plugins through a volume mount are
similar to the steps required to add any custom plugin to Edge Microgateway. When
you run the Docker container, you can mount the plugins directory on your local
system (the volume) on the container mount point, which is `/opt/apigee/plugins`.
You then specify the local volume directory in the Edge Microgateway configuration file.

Note: Volumes are used to persist data generated by and used by Docker containers. To learn more,
see <a href="https://docs.docker.com/storage/volumes/">Use volumes</a>.

The following steps illustrate how to use a Docker mount point to include custom
plugins.

1. Stop Edge Microgateway:

  ```
  edgemicro stop
  ```

1. Create a directory for you custom plugins. For example, create

  ```
  $HOME/edgemicro/custom/plugins
  ```
  
1. Add the custom plugin directory to the Edge Microgateway config file. For example:

  ```
    plugins:
      dir: $HOME/edgemicro/custom/plugins
      sequence:
        - oauth
        - response-uppercase
    ````

1. Write and test your plugin, according to the directions in [Write a simple plugin](/api-platform/microgateway/2.5.x/develop-custom-plugins#writeasimpleplugin). Be sure to
place your plugin code in the proper directory structure. For example:

  ```
  custom
    |
    |-- plugins
      |
      |- response-uppercase
      |     |- index.js
      |     |- package.json
      |- request-headers
      |     | - index.js
            | - package.json
  ```


3. Run the Docker container with a command similar to the following, where you
use the `-v` option to mount the plugins directory on the Docker volume. In the
following example command, the plugins directory `$HOME/edgemicro/custom/plugins`
(where the custom plugin is located) is mapped to the container's mount point `/opt/apigee/plugins`:

  ```
  docker run -P -p 8000:8000 -d --name edgemicro \
  -v /var/tmp:/opt/apigee/logs \
  -v $HOME/edgemicro/custom/plugins:/opt/apigee/plugins \
  -e EDGEMICRO_PROCESSES=1 \
  -e EDGEMICRO_ORG=jdoe \
  -e EDGEMICRO_ENV=test \
  -e EDGEMICRO_KEY=39c4b561100cd7f258768d1072f3e1d7c17b5f36a18fe89972bb5c9ce7e58fb \
  -e EDGEMICRO_SECRET=f5f9e239a38b4e6cc99c2aa067716a84aebdcff9580a7925fc500e402b1a5fa \
  -e EDGEMICRO_CONFIG=$EDGEMICRO_CONFIG \
  -e SERVICE_NAME=edgemicro \
  --security-opt=no-new-privileges \
  --cap-drop=ALL gcr.io/apigee-microgateway/edgemicro:latest
  ```

4. Call your API to test the plugin.


For more information, see <a href="https://docs.docker.com/engine/reference/run/#volume-shared-filesystems">VOLUME [shared filesystems]</a>.

### Option B: Build the plugins into the container

In this option, you build the plugins into your container.

#### 1. Package your plugins

1. Write and test your plugin, according to the directions in [Write a simple plugin](/api-platform/microgateway/2.5.x/develop-custom-plugins#writeasimpleplugin).

1. Place your plugin code in the proper directory structure. Plugin directories must follow a set structure. The following example shows the structure you must follow, where `response-uppercase` and `request-headers` are the
names of folders containing custom plugin code (these names are examples only,
your folder names may differ):

  ```
  plugin
    |
    |-- plugins
      |
      |- response-uppercase
      |     |- index.js
      |     |- package.json
      |- request-headers
      |     | - index.js
            | - package.json
  ```

1. `cd` to the `plugin` folder. 

1. In the `plugin` folder, zip the entire `plugins` folder:

   <pre class="devsite-terminal">
   zip -r plugins.zip plugins/</pre>


#### 2. Create a Docker image

Next, create a Dockerfile to add your plugin code to an Edge Microgateway image.

1. In the same directory where the zip file is located, create a new file called `Dockerfile`.
1. Add the following code to `Dockerfile` and save the file:

  ```
  USER root
  RUN apk update && \
      apk upgrade && \
      apk add zipapk add zip && \
      mkdir /opt/apigee/customplugins && \
      chown apigee:apigee /opt/apigee/customplugins
  COPY plugins.zip /opt/apigee/customplugins
  RUN su - apigee -c "unzip /opt/apigee/customplugins/plugins.zip -d /opt/apigee/customplugins"
  EXPOSE 8000
  EXPOSE 8443
  USER apigee
  ENTRYPOINT ["entrypoint"]
  ```
  Note: The contents of the Dockerfile changed in Edge Microgateway version 2.5.27.
  Be sure to use the Dockerfile shown above if you are on version 2.5.27 or later.

1. Create a new Edge Microgateway Docker image with your plugins:

  <pre class="prettyprint">
  <code class="devsite-terminal">docker build -t <var>image-name</var> .</code>
  </pre>
  
  For example:
  
  <pre class="prettyprint">
  <code class="devsite-terminal">docker build -t edgemicroplugins .</code>
  </pre>


#### 3. Update the Edge Microgateway configuration

Now that the plugins are packaged, you need to add them to the Edge Microgateway
configuration file.

1. Open the Edge Microgateway configuration file in an editor:

  <pre>
  $HOME/.edgemicro/<var>org</var>-<var>env</var>-config.yaml
  </pre>
  
  For example:
  
  <pre class="devsite-terminal">
  vi $HOME/.edgemicro/myorg-test-config.yaml</pre>
  
1. Add the plugin directory to the configuration file. In the following example
the `dir` attribute specifies the location of the plugin code (which you specified
in the Dockerfile). You must also specify the name of the plugin directory, which
in the example below is `response-uppercase`. 

    ```
    edgemicro:
      ...
      plugins:
        dir: /opt/apigee/plugins
        sequence:
          - oauth
          - response-uppercase
    ```
     
#### 4. Start the microgateway

Finally, you must start the microgateway in the container.

1. Run the following command to base64-encode the Edge Microgateway configuration file
located in `$HOME/.edgemicro`:

  <pre class="devsite-terminal">
  export EDGEMICRO_CONFIG=`base64 $HOME/.edgemicro/<var>your_org</var>-<var>your_env</var>-config.yaml`</pre>

  where `your_org` and `your_env` are the organization and environment you used when you
  ran the `edgemicro config` command.
  
  Remember to place back-ticks (\`) around the command. For example:
  
  <pre class="devsite-terminal">
  export EDGEMICRO_CONFIG=`base64 $HOME/.edgemicro/docs-test-config.yaml`</pre>
  
1. Run Edge Microgateway as a container. The command sets several environment variables
that are used by the container runtime to start Edge Microgateway:


  <pre class="devsite-terminal">docker run -P -p 8000:8000 -d --name edgemicroplugins \
  -e EDGEMICRO_PLUGIN_DIR=/opt/apigee/customplugins/plugins \
  -e EDGEMICRO_ORG=<var>your_org</var> \
  -e EDGEMICRO_ENV=<var>your_env</var> \
  -e EDGEMICRO_KEY=<var>your_key</var> \
  -e EDGEMICRO_SECRET=<var>your_secret</var> \
  -e EDGEMICRO_CONFIG=$EDGEMICRO_CONFIG \
  -e SERVICE_NAME=edgemicroplugins <var>image_name</var></pre>
  
  For example:
  
  <pre class="devsite-terminal">docker run -P -p 8000:8000 -d --name edgemicroplugins \
  -e EDGEMICRO_PLUGIN_DIR=/opt/apigee/customplugins/plugins \
  -e EDGEMICRO_ORG=docs \
  -e EDGEMICRO_ENV=test \
  -e EDGEMICRO_KEY=d9c34e1aff68ed969273b016699eabf48780e4f652242e72fc88a23e21252cb0 \
  -e EDGEMICRO_SECRET=3bc95a71c86a3c8ce04137fbcb788158731t51dfc6cdec13b7c05aa0bd969430 \
  -e EDGEMICRO_CONFIG=$EDGEMICRO_CONFIG \
  -e SERVICE_NAME=edgemicroplugins edgemicroplugins</pre>
  
1. Call your API to test the plugin:

  Test that the plugin code executes by calling your API and verifying that the
  output is as expected:

  <pre class="devsite-terminal">curl -i http://localhost:8000/hello -H "x-api-key:<var>apikey</var>"</pre>

  For example, the `response-uppercase` plugin might return a response like this:

  <pre class="devsite-terminal">curl -i http://localhost:8000/hello -H "x-api-key:PydUKRDGIXRqF2xh4usn1FLHbhGKVIz"
      HELLO, WORLD!</pre>

 

{% endblock %}
