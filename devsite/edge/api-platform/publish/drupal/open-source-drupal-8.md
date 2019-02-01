{% extends "_base.html" %}{% block body %}

# Build your portal using Drupal 8 (Beta)

<aside class="note">The Apigee Edge module for Drupal 8 is in Beta. See <a href="/release/release-stages">Apigee release stages</a>.
<p>This is not an officially supported Google product. To file an issue, use the GitHub Issue Queue for the <a href="https://github.com/apigee/apigee-edge-drupal/issues" target="_blank">Apigee Edge module</a> or <a href="https://github.com/apigee/apigee-client-php/issues" target="_blank">Apigee Edge Client Library for PHP</a>. Contributions are appreciated, as outlined in the contribution guidelines for each project.</p></aside>

Drupal 8 provides a reliable open-source, enterprise-level content management system (CMS). Using the Drupal 8 portal development tools, you can build a fully customizable developer portal. Because Drupal 8 is open-source, you can extend and contribute to the Drupal 8 portal development framework and leverage the knowledge of the Drupal community.

The following sections describe how to use Drupal 8 to developer your portal.

<h2 id="d8-install-run">Install and run Drupal 8 in your local environment</h2>

The following section describe how to install and run Drupal 8 in your local environment.

Note: The Apigee Edge module requires Drupal 8.6.x or higher and PHP 7.1 or higher.

<h3 id="prerequisites">Prerequisites</h3>

To install and run Drupal 8 in your local development environment, you need to install the following tools:

<table>
  <tr>
   <td><a href="https://getcomposer.org/" target="_blank">Composer</a>
   </td>
   <td>Composer enables you to install Drupal modules with all of the required dependencies. Because the Apigee Edge module requires the <a href="https://github.com/apigee/apigee-client-php" target="_blank">Apigee Edge Client Library for PHP</a>, it is recommended that you install Drupal 8 and its modules using Composer to facilitate and manage these dependencies.
   </td>
  </tr>
  <tr>
   <td><a href="https://docs.devwithlando.io/" target="_blank">Lando</a>
   </td>
   <td>Lando enables you to set up the infrastructure needed to run a Drupal CMS in your local environment using <a href="https://www.docker.com/" target="_blank">Docker</a> containers.
   </td>
  </tr>

</table>

<h3 id="d8-install">Install Drupal 8</h3>


There are several options available for setting up Drupal 8 using Composer, as described in <a href="https://www.drupal.org/docs/develop/using-composer/using-composer-to-manage-drupal-site-dependencies" target="_blank">Using Composer to manage Drupal site dependencies</a>. The following procedure uses the <a href="https://github.com/drupal-composer/drupal-project" target="_blank">Composer template for Drupal projects</a>.

1. Open a terminal window and create a directory names `projects`.    
2. Change (cd) to the `projects` directory.        
3. Execute the following command:    
    **Note**: You may need to replace `composer` with `php composer.phar` in the following command example if Composer is not in your PATH.  See <a href="https://getcomposer.org/doc/00-intro.md#globally" target="_blanK">Installing composer globally</a> to move Composer in your PATH.
        
   ` composer create-project drupal-composer/drupal-project:8.x-dev d8-devportal --stability dev --no-interaction`

This command creates a new directory named **`d8-devportal`** in the `projects` directory and installs Drupal 8 and all dependencies in the **`web`** directory. Review the **`README`** file in the d8-devportal directory for more details about the installation.

<h3 id="d8-run">Run Drupal 8</h3>

To run Drupal, you need to install a web server configured with PHP so that server requests are passed to Drupal’s PHP code for processing and to generate a response. <a href="https://docs.devwithlando.io/" target="_blank">Lando</a> enables you to quickly set up a Nginx web server already configured with PHP to host Drupal in your local environment  using <a href="https://www.docker.com/" target="_blank">Docker</a> containers.

Run the `lando init` command and respond to the prompts (shown in **bold**). This commands creates a `.lando.yml` file in the root directory.   
<pre>$ <b>cd d8-devportal</b>
$ <b>lando init --recipe=drupal8</b>
? Where is your webroot relative to the init destination?<b> web</b>
? What do you want to call this app? <b>devportal</b>
</pre>

```
NOW WE'RE COOKING WITH FIRE!!!
Your app has been initialized!

Go to the directory where your app was initialized and run
`lando start` to get rolling.

Check the LOCATION printed below if you are unsure where to go.

Here are some vitals:

 NAME      devportal                                           
 LOCATION  /Users/chet/projects/d8-devportal             
 RECIPE    drupal8                                             
 DOCS      https://docs.devwithlando.io/tutorials/drupal8.html 
```


Run the following command to start the server:         

**`$ lando start`**

> Note: Before starting the server, you may want to disable metrics tracking in the `~/.lando/config.yml` file. 

The command prints out the URLs for the application. Copy the the non-HTTPS hostname URL and open it in your browser to run the installer. For example:    
**`http://devportal.lndo.site`**

Respond to the prompts to configure and install the server.     
> Note: Run `lando info` to view the database host name, username, password, and connection URLs.

| Configuration | Description |
|---------------|---------------|
| Choose language | Choose your language from the drop-down (for example, **English**) and click **Save and continue**. |
| Choose profile | Select the **Standard** installation profile and select **Save and continue**. |
| Set up database | Enter the following values to set up the database: <ul><li>Database type: <b>MySQL, MariaDB, Percona Server, or equivalent</b></li><li>Database name: <b>drupal8</b></li><li>Database username: <b>drupal8</b></li><li>Database password: <b>drupal8</b></li><li>Advanced Options-Host: <b>database</b></ul> Click **Save and continue**. |
| Install site | A progress bar displays while the site is installed. |
| Configure site | Configure your site including administrator login and password information, as required, and click **Save and continue**. | 

The default home page is displayed.    

<img src="/api-platform/images/drupal-site.png" width="50%">

Next, you need to install the Apigee Edge module to integrate your Drupal site with Apigee Edge.

<h2 id="d8-edge-integration">Install and configure the Apigee Edge module</h2>


Integrate your Drupal 8 developer portal with Apigee by installing and configuring the **Apigee Edge module**. 

<img alt="Integration with Apigee Edge" src="/api-platform/images/portal-edge.png" width="50%">

The developer portal does not function as a stand-alone system. Much of the information used by the portal is actually stored on Apigee Edge. 

The Apigee Edge module that is installed on the portal acts as a client for Apigee Edge. Using the credentials that you configure, the portal initiates communication with Edge by making REST requests over HTTP and HTTPS. For example, when a developer registers a new app on the portal, the portal makes a request to Edge to send information about the app to Edge.

To install and configure the Apigee Edge module:

1. In the terminal window, from the d8-devportal directory install the Apigee Edge module using Composer:    
    `composer require drupal/apigee_edge`    

2. In your browser, click **Extend** in the Drupal administration menu.    
3. Select the **Apigee Edge** module.    
    Optionally, enable the following modules:    
    * **Apigee Edge: API Product RBAC** to enable admins to <a href="https://www.drupal.org/docs/8/modules/apigee-edge/configure-access-permissions-to-api-products#by-apiproduct" target="_blank">configure access permissions to API products individually</a>.    
    * **Debug helper Apigee Edge module** to include additional debugging information in the logs.        
4. Click **Install**.    
5. <a href="https://www.drupal.org/docs/8/modules/apigee-edge/configure-the-connection-to-apigee-edge" target="_blank">Configure the connection to Apigee Edge</a>.

<h2 id="d8-build">Build your portal</h2>

Build your portal using Drupal 8. For assistance, refer to the following sources:

*   <a href="https://www.drupal.org/docs/8" target="_blank">Drupal 8 Documentation</a>
*   <a href="https://www.drupal.org/community" target="_blank">Drupal Community</a>
*   Third-party with expertise in Drupal 8

<h2 id="d8-publish-apis">Publish your API documentation</h2>

To render your OpenAPI Specification in Swagger UI, you can use the **Swagger UI Field Formatter** module. This module can be used to publish API Documentation created using Swagger or Open API Specification, on any Drupal site.

For more information, see <a href="https://www.drupal.org/docs/8/modules/apigee-edge/document-your-apis-using-the-swagger-ui" target="_blank">Document your APIs using the Swagger UI</a> on the Drupal.org site.

<h2 id="d8-hosting-provider">Choose your hosting provider</h2>

Choose a provider to host your developer portal, such as <a href="https://pantheon.io/" target="_blank">Pantheon</a>, <a href="https://www.acquia.com/" target="_blank">Acquia</a>, or another provider.

<h2 id="d8-pantheon">Get started with Pantheon</h2>

This section describes how to set up a local Pantheon site and install the Apigee Edge module on <a href="https://pantheon.io/docs/guides/drupal-8-composer-no-ci/#addsearch=git%20sftp%20terminus" target="_blank">Pantheon</a>.

To install the Apigee Edge module you must use <a href="https://getcomposer.org/" target="_blank">Composer</a> to ensure all dependencies are downloaded and installed. Since you cannot run Composer directly from your Pantheon site, you need to check in to your site all files downloaded by Composer. This is required only if you are not able to run Composer directly on the server hosting your site, which is the case for Pantheon sites.

You can use one of the following methods to set up Pantheon as your hosting provider.

<table>
  <tr>
   <td><strong>Method</strong>
   </td>
	 <td><strong>Description</strong></td>
  </tr>
  <tr>
    <td><a href="#setup-without-contiguous-integration">Composer without contiguous integration</a>
   </td>
   <td>Set up a local Pantheon site quickly using Composer. Push updates to Patheon. This method is described in this section.
<p>
<strong>Note</strong>: You will not be able to <a href="https://pantheon.io/docs/core-updates/" target="_blank">apply Drupal core updates using the Drupal site dashboard</a>. To manage production environments, this method is recommended for advanced users only.
   </td>
  </tr>
  <tr>
   <td>Composer with contiguous integration
   </td>
   <td>Set up a development site to make it easy for your team to follow development best practices, such as using pull requests and automated testing. For more information, see the <a href="https://pantheon.io/docs/guides/build-tools/" target="_blank">Pantheon build tools guide</a>.
   </td>
  </tr>
</table>

<h3 id="setup-without-contiguous-integration">Set up a Drupal site on Pantheon using Composer without contiguous integration</h3>

> Note: Ensure that you have installed <a href="https://getcomposer.org/" target="_blank">Composer</a> and <a href="https://pantheon.io/docs/terminus/install/" target="_blank">Terminus</a>.

To set up a new Drupal site on Pantheon, in <a href="https://pantheon.io/docs/guides/drupal-8-composer-no-ci/#addsearch=git%20sftp%20terminus" target="_blank">Drupal 8 and Composer on Pantheon Without Continuous Integration</a> complete the steps up to and including "Installing Drupal."  The other steps in the section are not required to set up a new Drupal site on Pantheon, but will help you understand the workflow for other tasks.

<h3 id="install-module-pantheon">Install Apigee Edge Module</h3>

To install the Apigee Edge module, use Composer. Pull down (git clone) the latest code to your local machine and run Composer: 

```
composer require drupal/apigee_edge
```


> Note: If you get an error reporting an incompatible PHP version, use the `--ignore-platform-reqs` flag to ignore the version:     
> `composer require drupal/apigee_edge --ignore-platform-reqs`

Check the `pantheon.yml` file in your root directory to ensure that the PHP version is set to 7.1 or higher, which is required by the Apigee Edge module. PHP 7.1 and 7.2 are both supported by Pantheon:


```
api_version: 1
web_docroot: true
php_version: 7.2
...
```


Add the new files to the repo by executing the following commands:

```
git add .
git commit -m 'Added Apigee Edge Module'
git push 
```

Enable the Apigee Edge module using one of the following methods:

* Log in to your Drupal site as an administrator, select **Extend**, select the **Apigee Edge** module, and click **Install**.
* Run the following Terminus command (replace `$PANTHEON_SITE_NAME` with the actual value, in this case):    
    `terminus drush $PANTHEON_SITE_NAME.dev -- en -y apigee_edge `


{% endblock %}
