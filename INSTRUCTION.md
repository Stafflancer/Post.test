Use composer for download project components.
Install profile "configuration installer"

For the task we used default Drupal content type Article.

// Create Article
If you need to add new Article send POST request to the endpoint http://[domain]/jsonapi/node/article,
with headers:
Accept: application/vnd.api+json
Content-Type: application/vnd.api+json
and use Basic Authorization with drupal admin credentials (for example admin:admin)
and with request json body:
{
    "data": {
        "type": "node--article",
        "attributes": {
            "title": "Test",
            "body": {
                "value": "Test",
                "format": "basic_html"
            }
        }
    }
}
You can use curl command
curl \
    --user admin:admin \
    --header 'Accept: application/vnd.api+json' \
    --header 'Content-type: application/vnd.api+json' \
    --data '{ "data": { "type": "node--article", "attributes": { "title": "Test", "body": { "value": "Test", "format": "basic_html" }}}}' \
    --request POST http://test.localhost/jsonapi/node/article

// Get Article
If you want get information about article you need to send GET request to endpoint http://[domain]/jsonapi/node/article/{{article_uuid}}
with headers above
where {{article_uuid}} - uuid of the node

// Edit Article
If you want to update article you need to send PATCH request to endpoint http://[domain]/jsonapi/node/article/{{article_uuid}}
with new values in the body:
{
    "data": {
      "type": "node--article",
      "id": "{{article_uuid}}",
      "attributes": {
        "title": "My updated title"
      }
    }
}

// Remove Article
If you want to remove article you need to send DELETE request to endpoint http://[domain]/jsonapi/node/article/{{article_uuid}}

// Add tag to the Article
If you need to add new tag to the article you need to create taxonomy term and then update the article:
1. Send POST request to the endpoint http://[domain]/jsonapi/taxonomy_term/tags
with body:
{
    "data": {
        "type": "taxonomy_term--tags",
        "attributes": {
            "name": "Test"
        }
    }
}
and headers:
Accept: application/vnd.api+json
Content-Type: application/vnd.api+json

curl \
    --user admin:admin \
    --header 'Accept: application/vnd.api+json' \
    --header 'Content-type: application/vnd.api+json' \
    --data '{ "data": { "type": "taxonomy_term--tags", "attributes": { "name": "Test"}}}' \
    --request POST http://[domain]/jsonapi/taxonomy_term/tags

2. In the response you need to get uuid and update the node with next request:
curl \
    --user admin:admin \
    --header 'Accept: application/vnd.api+json' \
    --header 'Content-type: application/vnd.api+json' \
    --data '{ "data": { "type": "node--article", "id": "{{article_uuid}}", "relationships": { "field_tags": { "data": { "type": "taxonomy_term--tags", "id": "{{taxonomy_term_uuid}}" }}}}}' \
    --request PATCH http://test.localhost/jsonapi/node/article/{{article_uuid}}

// Add image
If you want to add image you need to add entity Media and after update the node Article with {{media_uuid}}
Use headers:
Content-Type: application/vnd.api+json
Accept: application/vnd.api+json

For create Media you need to send POST request with next body:
{
    "data": {
      "type": "media--image",
      "attributes": {
          "status": true,
          "name": "Test"
      },
      "relationships": {
          "field_media_image": {
              "data": {
                  "type": "file--file",
                  "id": "{{file_uuid}}",
                }
            }
        }
    }
}
In the response you need to get uuid of the Media and update the node with next request body use PATCH method:
{
    "data": {
        "type": "node--article",
        "id": "{{article_uuid}}",
        "relationships": {
            "field_image": {
                "data": {
                    "type": "media--image",
                    "id": "{{media_uuid}}"
                }
            }
        }
    }
}
