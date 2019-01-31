{% extends "_base.html" %}
{% block title %}Extensions reference overview{% endblock %}
{% block body %}

{{ extensions_availability }}

Apigee Edge includes several extensions designed to integrate the functionality of external resources into your API proxies. This page lists references for included extensions.

These topics provide reference for configuring and using each extension. For steps to configure an extension using the Apigee console, see [Adding and configuring an extension](/api-platform/extensions/configuring-an-extension).

For more about how you can use extensions to integrate external resources with your API proxies, see [What are extensions?](/api-platform/extensions/extensions-overview).

For a hands-on introduction to extensions, see [Tutorial: Adding and using an extension](/api-platform/tutorials/add-extension/intro).

Extension | Description
------------ | -------------
[Google Cloud Data Loss Prevention Extension](/api-platform/reference/extensions/google-cloud-data-loss-prevention-extension) | Obscure sensitive data from content and images.
[Google Cloud Firestore Extension](/api-platform/reference/extensions/google-cloud-firestore-extension) | Create, read, or delete data in a Cloud Firestore database.
[Google Cloud Natural Language Extension](/api-platform/reference/extensions/google-cloud-natural-language-extension) | Classify content by applying categories, as well as to analyze content sentiment on a positive to negative scale.
[Google Cloud Pub/Sub Extension](/api-platform/reference/extensions/google-cloud-pub-sub/google-cloud-pub-sub-extension-changelog) | Publish messages to a Google Cloud Pub/Sub topic.
[Google Cloud Spanner Database Extension](/api-platform/reference/extensions/google-cloud-spanner-database-extension) | Perform insert, update, and query operations on a Cloud Spanner database.
[Google Cloud Storage Extension](/api-platform/reference/extensions/google-cloud-storage-extension) | Consume content stored in a Cloud Storage bucket. This extension requires that a Service Account JSON key file be supplied for client authentication.
[Google Cloud Vision Extension](/api-platform/reference/extensions/google-cloud-vision-extension) | Analyze images to understand their content.
[Google Stackdriver Logging Extension](/api-platform/reference/extensions/google-stackdriver-logging-extension) | Write entries to Stackdriver Logging logs.
[Informatica Integration Cloud Extension](/api-platform/reference/extensions/informatica-integration-extension) | Retrieve and execute Informatica business and integration processes.

{% endblock %}
