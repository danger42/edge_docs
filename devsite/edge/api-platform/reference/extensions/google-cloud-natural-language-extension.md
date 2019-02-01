{% extends "_base.html" %}
{% block title %}Google Cloud Natural Language Extension{% endblock %}
{% block body %}

{{ extensions_availability }}

Provides access to the [Google Cloud Natural Language API](https://cloud.google.com/natural-language/docs/).

Use this extension to classify content by applying categories, as well as to analyze content sentiment on a positive to negative scale.

Note: To use the Natural Language Extension, you'll need a GCP Service Account JSON key file with Natural Language APIs enabled.

This content provides reference for configuring and using this extension. For steps to configure an extension using the Apigee console, see [Adding and configuring an extension](/api-platform/extensions/configuring-an-extension).

## Actions

### classifyContent

Analyzes a portion of text and returns a list of content categories that apply to the text.

For more on this feature in the Natural Language API, see [Classifying Content](https://cloud.google.com/natural-language/docs/classifying-text#language-classify-content-nodejs) and [Content Categories](https://cloud.google.com/natural-language/docs/categories).

#### Request parameters

```xml
<Input><![CDATA[{
  "data" : "content-to-analyze"
}]]></Input>
```

Parameter | Description | Type | Default | Required
--------- | ----------- | ---- | ------- | --------
data | The content to analyze. | String | None. | Yes.

#### Response

A [Categories](https://cloud.google.com/natural-language/docs/categories) array in which each item contains a category for classifying the content and a confidence score.

### sentenceSentiment

Analyzes the given text and identifies the prevailing emotional opinion within it, especially to determine a writer's attitude as positive, negative, or neutral.

For more on this feature in the Natural Language API, see [Analyzing Sentiment](https://cloud.google.com/natural-language/docs/analyzing-sentiment) and [Natural Language API Basics](https://cloud.google.com/natural-language/docs/basics#interpreting_sentiment_analysis_values).

#### Request parameters

```xml
<Input><![CDATA[{
  "data" : "content-to-analyze"
}]]></Input>
```

Parameter | Description | Type | Default | Required
--------- | ----------- | ---- | ------- | --------
data | The content to analyze. | String | None. | Yes.

#### Response

An object containing sentiment scores for the document overall and each of its sentences (in an array). For both the document and its sentences, the following properties are included:

- A Content property with the content analyzed.
- A Score property indicating the content's sentiment on a scale between 1 and -1, from positive (positive number) to negative (negative number).
- A Magnitude property indicating the overall strength of emotion (both positive and negative) within the content, between 0.0 and +inf.

### entitySentiment

Analyzes the specified text to determine the sentiment (positive or negative) expressed about each [entity](https://cloud.google.com/natural-language/docs/reference/rest/v1/Entity) within the text. Entity sentiment is represented by numerical score and magnitude values and is determined for each mention of an entity. Those scores are then aggregated into an overall sentiment score and magnitude for an entity.

For more about entity analysis in the Google Cloud Natural Language API, see [Analyzing Entity Sentiment](https://cloud.google.com/natural-language/docs/analyzing-entity-sentiment) and [Interpreting sentiment analysis values](https://cloud.google.com/natural-language/docs/basics#interpreting_sentiment_analysis_values).

#### Request parameters

```xml
<Input><![CDATA[{
  "data" : "content-to-analyze"
}]]></Input>
```

Parameter | Description | Type | Default | Required
--------- | ----------- | ---- | ------- | --------
data | The content to analyze. | String | None. | Yes.

#### Response

An array of objects containing sentiment scores for entities in the text. For both the document and its sentences, the following properties are included:

- A Name property with .
- A [Type](https://cloud.google.com/natural-language/docs/reference/rest/v1/Entity#Type) property describing the entity's type.
- A Score property indicating the entity's sentiment on a scale between 1 and -1, from positive (positive number) to negative (negative number).
- A Magnitude property indicating the strength of emotion (both positive and negative) for the entity, between 0.0 and +inf.

## Configuration Reference

Use the following when you're configuring and deploying this extension for use in API proxies.

Note: The ability to configure and deploy extensions is available only to system administrators.

### Common extension properties

{% dynamic include /includes/___common-extension-properties %}

### Properties for this extension package

Specify values for the following configuration properties specific to this extension.

Property | Description | Default | Required
-------- | ----------- | ------- | --------
credentials | When entered in the Apigee Edge console, this is the contents of your [service account key file](https://cloud.google.com/iam/docs/creating-managing-service-account-keys). When sent via the management API, it is a base64-encoded value generated from the service account key file. | None. | Yes.

{% endblock %}
