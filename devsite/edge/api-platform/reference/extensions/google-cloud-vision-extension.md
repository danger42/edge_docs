{% extends "_base.html" %}
{% block title %}Google Cloud Vision Extension{% endblock %}
{% block body %}

{{ extensions_availability }}

Discover the content and text in images using machine learning models.

This content provides reference for configuring and using this extension. Before using this extension from an API proxy, you must:

1. [Enable the Cloud Vision API](https://cloud.google.com/vision/docs/before-you-begin) for your service account. 

1. If you'll be using Cloud Storage as the source of your images, you'll also need to grant access for this extension to Cloud Storage as described in the [Google Cloud Storage Extension reference](/api-platform/reference/extensions/google-cloud-storage-extension).

1. When you have a service account that has permission for Cloud Vision (and Cloud Storage, if you're using it), [generate a key for the service account](https://cloud.google.com/iam/docs/creating-managing-service-account-keys). 

1. Use the contents of the resulting key JSON file when [adding and configuring](/api-platform/extensions/configuring-an-extension) the extension using the [configuration reference](#configuration_reference).

## About Cloud Vision

The [Google Cloud Vision API](https://cloud.google.com/vision/docs/) uses machine learning models to analyze images. You can [train a model](https://cloud.google.com/vision/automl/docs/) for the API to use or [use the model that's built-in](https://cloud.google.com/vision/docs/).

Using the built-in model, Cloud Vision classifies images into categories such as "skyscraper", sailboat", "lion", or "Eiffel Tower". It detects objects, faces, logos, and landmarks within images, and locates words contained within images.

## Samples

The following examples illustrate how to configure support for Cloud Vision extension actions using the [ExtensionCallout policy](/api-platform/reference/policies/extension-callout-policy).

  * {Detect labels}

    In the following example, the extension's `detectLabels` action gets the image at the `image_uri` and passes it to the Cloud Vision API for analysis. The API will examine the image and determine which labels apply to the content of the image.

    ```xml
    <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
    <ConnectorCallout async="false" continueOnError="true" enabled="true" name="Cloud-Vision-Extension">
        <DisplayName>Cloud Vision Extension</DisplayName>
        <Connector>cloud-vision-extension-example</Connector>
        <Action>detectLabels</Action>
        <Input><![CDATA[
        {
            "image_uri" : "gs://cloud-vision-example/empire-state-building.jpg"
        }
        ]]></Input>
        <Output>vision.labels.retrieved</Output>
    </ConnectorCallout>
    ```

    The following [Assign Message](/api-platform/reference/policies/assign-message-policy) policy uses the value of the variable storing the extension's response to assign the response payload.

    ```xml
    <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
    <AssignMessage async="false" continueOnError="false" enabled="true" name="Get-Image-Labels">
        <DisplayName>Get Image Labels</DisplayName>
        <AssignTo type="response" createNew="false"/>
        <Set>
            <Payload contentType="application/json">{vision.labels.retrieved}</Payload>
        </Set>
    </AssignMessage>
    ```

    Given an image of an urban area with a very tall building in it, you might receive a response such as the following:

    ```json
    {
      "labels": [
          {
              "locations": [

              ],
              "properties": [

              ],
              "mid": "/m/0j_s4",
              "locale": "",
              "description": "metropolitan area",
              "score": 0.9868549704551697,
              "confidence": 0,
              "topicality": 0.9868549704551697,
              "boundingPoly": null
          },
          {
              "locations": [

              ],
              "properties": [

              ],
              "mid": "/m/079cl",
              "locale": "",
              "description": "skyscraper",
              "score": 0.966157853603363,
              "confidence": 0,
              "topicality": 0.966157853603363,
              "boundingPoly": null
          }
      ]
    }
    ```

  * {Detect text}
  
    In the following example, the extensions `detectText` action gets the image at the `image_uri` and passes it to the Cloud Vision API for analysis. The API will examine the image, identifying text in the image.

    ```xml
    <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
    <ConnectorCallout async="false" continueOnError="true" enabled="true" name="Cloud-Vision-Text">
        <DisplayName>Cloud Vision Text</DisplayName>
        <Connector>cloud-vision-extension-example</Connector>
        <Action>detectText</Action>
        <Input><![CDATA[
        {
            "image_uri" : "gs://cloud-vision-example/parking-signs1.jpg"
        }
        ]]></Input>
        <Output>vision.text.retrieved</Output>
    </ConnectorCallout>
    ```

    The following [Assign Message](/api-platform/reference/policies/assign-message-policy) policy uses the value of the variable storing the extension's response to assign the response payload.

    ```xml
    <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
    <AssignMessage async="false" continueOnError="false" enabled="true" name="Get-Image-Text">
        <DisplayName>Get Image Text</DisplayName>
        <AssignTo type="response" createNew="false"/>
        <Set>
            <Payload contentType="application/json">{vision.text.retrieved}</Payload>
        </Set>
    </AssignMessage>
    ```

    Given an image containing signs in a parking log, you might receive a response such as the following:

    ```json
    {
      "text": [
          {
              "locations": [

              ],
              "properties": [

              ],
              "mid": "",
              "locale": "en",
              "description": "RESERVED\nVISITORPARKING\nPARKING\nONLY>\n$150 FINE\n",
              "score": 0,
              "confidence": 0,
              "topicality": 0,
              "boundingPoly": {
                  "vertices": [
                      {
                          "x": 64,
                          "y": 56
                      },
                      {
                          "x": 378,
                          "y": 56
                      },
                      {
                          "x": 378,
                          "y": 218
                      },
                      {
                          "x": 64,
                          "y": 218
                      }
                  ]
              }
          },
          {
              "locations": [

              ],
              "properties": [

              ],
              "mid": "",
              "locale": "",
              "description": "RESERVED",
              "score": 0,
              "confidence": 0,
              "topicality": 0,
              "boundingPoly": {
                  "vertices": [
                      {
                          "x": 243,
                          "y": 56
                      },
                      {
                          "x": 378,
                          "y": 56
                      },
                      {
                          "x": 378,
                          "y": 84
                      },
                      {
                          "x": 243,
                          "y": 84
                      }
                  ]
              }
          }
       ]
    }
    ```

## Actions

### detectLabels

Detects and extracts information about entities within the specified image. Detected entities range across a broad group of categories. For example, use this action to identify objects, locations, activities, animal species, products, and more.

Also, be sure to see the [Cloud Vision API documentation](https://cloud.google.com/vision/docs/labels).

#### Request parameters

Parameter | Description | Type | Default | Required
--------- | ----------- | ---- | ------- | --------
[image_uri](https://cloud.google.com/vision/docs/reference/rest/v1/images/annotate#imagesource) | Source of the image. This can be from the Internet or Google Cloud Storage (format: `gs://bucketname/filename`). If the source is Google Cloud Storage, the image file must be public. | String | None. | Yes.

  * {Syntax}

    ```xml
    <Input><![CDATA[{
        "image_uri" : "uri-of-image-to-analyze"
    }
    ]]></Input>
    ```
    
  * {Example}

    In the following example, the extension's `detectLabels` action sends the specified image to the Vision API for analysis.

    ```xml
    <Input><![CDATA[
    {
        "image_uri" : "gs://cloud-vision-example/empire-state-building.jpg"
    }
    ]]></Input>
    ```

#### Response

An object containing a `labels` array of labels that represent entities detected within the image. For more, see [Detect labels](https://cloud.google.com/vision/docs/labels).

### detectText

Detects and extracts text from the specified image.

#### Request parameters

Parameter | Description | Type | Default | Required
--------- | ----------- | ---- | ------- | --------
[image_uri](https://cloud.google.com/vision/docs/reference/rest/v1/images/annotate#imagesource) | Source of the image. This can be from the Internet or Google Cloud Storage (format: `gs://bucketname/filename`). If the source is Google Cloud Storage, the image file must be public. | String | None. | Yes.

  * {Syntax}

    ```xml
    <Input><![CDATA[
    {
        "image_uri" : "uri-of-image-to-analyze"
    }
    ]]></Input>
    ```

  * {Example}

    In the following example, the extension's `detectText` action sends the specified image to the Vision API for analysis.

    ```xml
    <Input><![CDATA[
    {
        "image_uri" : "gs://cloud-vision-example/parking-signs1.jpg"
    }
    ]]></Input>
    ```

#### Response

An object containing a `text` array of the text detected.  For more, see [Detect labels](https://cloud.google.com/vision/docs/ocr).

## Configuration Reference

Use the following when you're configuring and deploying this extension for use in API proxies. For steps to configure an extension using the Apigee console, see [Adding and configuring an extension](/api-platform/extensions/configuring-an-extension).

Note: The ability to configure and deploy extensions is available only to system administrators.

### Common extension properties

{% dynamic include /includes/___common-extension-properties %}

{% endblock %}
