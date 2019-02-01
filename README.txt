******************************
*           README           *
******************************
This GitHub repo contains the source documentation for the SAP release of Apigee
Edge. It contains a subset of files used by the Apigee docs developer site
(https://docs.apigee.com).

Doc drop:
https://github.com/danger42/edge_docs.git

SAP-specific product updates/known issues:
4.19.01: TBD
Earlier:
4.18.05: https://docs.google.com/document/d/1BhvTzgEChrV-jJpAcxcVNlXaG_rF2lhzXZJoqYLcD20

Release Notes 
4.19.01: https://docs.apigee.com/release/notes/41901-edge-private-cloud-release-notes
Earlier:
4.18.05: https://docs.apigee.com/release/notes/41805-edge-private-cloud-release-notes

******************************
*       What’s included      *
******************************

This archive includes the following files:

  1. /devsite/*

     Devsite source files, beginning with their state as of 
     Private Cloud release version 4.18.05. Content files in our CMS
     ("devsite") contain a mixture of the following markup:
       - HTML
       - Jinja2 (http://jinja.pocoo.org/docs/2.10/)
       - Markdown (https://daringfireball.net/projects/markdown/syntax)

     This directory also includes PDFs, images, CSS, "helper" files, and included
     files.

  2. /devsite/edge/includes/*

     Contains files that are included within another file (see below for more info)
     
  2. /devsite/_localvars.html 
  
     Contains variable definitions that should replace {{variable_name}} (see below)

  3. /drupal-node-to-file-mapping.csv

     Maps Drupal nodes to new files in CSV format. Columns are:
       - node_id          Drupal node ID in old system
       - title            Page title
       - old_alias        Drupal alias in old system
       - old_path         Drupal path in old system
       - new_alias        File location in new CMS
       - new_path

  4. /README.txt

  5. /license.txt

This may not be a comprehensive list of all files included in the archive; it's
provided as informational.

******************************
*     What’s NOT included    *
******************************
The following files are not included in this archive:
1. Release info (such as release notes and deprecation policy)
2. Older versions of OPDK
3. Metadata files (such as base, project, and redirects)
4. Monetization Services docs
5. API BaaS docs
6. Sense docs
7. AWS and Cloud Foundry integrations docs
8. Advisory docs
9. Management API docs
10. Istio adapter docs
11. Integrations docs

As with the included files, this is informational and not mean to be a
complete list.

******************************
*         Doc Updates        *
******************************
This section lists some of the changes to the documentation structure and
contents of the docs sent to SAP.

1. Markup languages has been updated to use a new version of the Google CMS.
   Changes include using Jinja rather than Django.

2. Apigee antipatterns content (formerly a PDF) is now included
   (/edge/troubleshoot/antipatterns/).

3. Apigee Extensions doc is now included (/edge/api-platform/extensions/).

4. Tutorials are now included (/edge/api-platform/tutorials/).

******************************
*      Formatting Notes      *
******************************
This section describes some of our formatting, so that you might make better
sense out of it if you look at the source.

1. SSIs/includes:
   {% include ... %}
   {% dynamic include ... %}
   {% includecode ... %}

   These directives insert the contents of another file into the current file.
   Included files are in the /devsite/edge/includes directory of the repo. In some
   cases, an included file is used more than once.

2. Variable values:
   {{ variable_name }}

   Variable names in double braces {{ ... }} are replace by their values that
   are defined in the _localvars.html file (at /devsite).

3. Custom tags:
   <var>...</var>                  Bold, red, italic character style
   <aside>                         Notes/warning/tip with icons & inside boxes
   <section class='expandable'>    "Zippys" or drop down panels
   <dl><dd>...</dd></dl>           "Definition lists", which are a sort of list
                                   without bullets

4. Metadata/page wrappers:
   {% extends "_base.html" %}{% block title %}some_title{% endblock %}{% block body %}

   Jinja2 header that defines the opening <html> and <body> tags.

   {% endblock %}

   Jinja2 footer that defines the closing </body> and </html> tags.

5. Styles:
   Apigee-specific CSS styles include:
     devsite-terminal      Inserts a "$" for commands inside <pre> tags
     prettyprint           Adds colorization for <pre> tags
     external              Adds a "linkbox" prompt to <a> tags
     hide-from-toc         Prevents <h2> and <h3> values from showing up in TOC

******************************
*        Questions?          *
******************************
Feel free to reach out to nickdanger@google.com with questions.
