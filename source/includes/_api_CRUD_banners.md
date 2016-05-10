## Banner - Operations 

*   [List Banners](#list-blog-posts)
*   [Get a Banner](#get-a-blog-post)
*   [Create a Banner](#create-a-blog-post)
*   [Update a Banner](#update-a-blog-post)

## List Banners

Gets the banners available. (Default sorting is by banner id, from lowest to highest.)

*   [OAuth](#create-a-banner)
>`GET /stores/{store_hash}/v2/banners`
*   [Basic Auth](#create-a-banner)
>`GET /api/v2/banners`

### Filters

Filter parameters can be added to the URL query string to select specific banners in the collection.

| Parameter | Type | Example                          |
| --------- | ---- | -------------------------------- |
| min_id  | int  | /api/v2/banners?min_id={value} |
| max_id  | int  | /api/v2/banners?max_id={value} |

Parameters can be added to the URL query string to paginate the collection. The maximum limit is 250. If a limit isn't provided, up to 50 banners are returned by default.

| Parameter | Type | Example                         |
| --------- | ---- | ------------------------------- |
| page    | int  | /api/v2/banners?page={number} |
| limit   | int  | /api/v2/banners?limit={count} |

### Response

Example JSON returned in the response:
```
    {
      "banners": {
        "banner": [
          {
            "id": "1",
            "name": "Grand Closing Sale",
            "page": "home_page",
            "content": "Thanks for shopping with us.",
            "date_to": "1393218000",
            "item_id": "0",
            "visible": "0",
            "location": "top",
            "date_from": "1391576400",
            "date_type": "custom",
            "date_created": "1392073898"
          },
          {
            "id": "2",
            "name": "New Year Nerd Bling",
            "page": "category_page",
            "content": "Discount on Apparel for Orders of $20 or more to Celebrate 2014. Use code NRDBLING14 at Checkout.",
            "date_to": "1394427600",
            "item_id": "30",
            "visible": "0",
            "location": "top",
            "date_from": "1392008400",
            "date_type": "custom",
            "date_created": "1392073935"
          },
          {
            "id": "3",
            "name": "Test",
            "page": "home_page",
            "content": "The latest in popular board games. Game night fun for the whole family.",
            "date_to": "0",
            "item_id": "0",
            "visible": "0",
            "location": "top",
            "date_from": "0",
            "date_type": "always",
            "date_created": "1429485000"
          },
          {
            "id": "4",
            "name": "Hi, Jason",
            "page": "home_page",
            "content": "Swanky",
            "date_to": "0",
            "item_id": "0",
            "visible": "0",
            "location": "top",
            "date_from": "0",
            "date_type": "always",
            "date_created": "1441858037"
          }
        ]
      }
    }
```
  
## Get a Banner

Retrieves a specified banner.

*   [OAuth](#create-a-banner)
>`GET /stores/{store_hash}/v2/banners/{id}`
*   [Basic Auth](#create-a-banner)
>`GET /api/v2/banners`

GET /api/v2/banners/{id}

### Response

Example JSON returned in the response:
```
    {
      "banner": {
        "id": "4",
        "name": "Hi, Jason",
        "page": "home_page",
        "content": "Swanky",
        "date_to": "0",
        "item_id": "0",
        "visible": "0",
        "location": "top",
        "date_from": "0",
        "date_type": "always",
        "date_created": "1441858037"
      }
    }
```
  
## Create a Banner

Creates a new banner.

POST /stores/{store_hash}/v2/banners

POST /api/v2/banners

### Read-only Properties

The following properties of the banner are read-only. If one or more of these properties are included in the request, it will be rejected.

### Requirements

The following properties of the banner are required. The request won't be fulfilled unless these properties are valid.

* name
* content
* page
* location
* date_type

### Response

Example JSON returned in the response:

```
    {
      "banner": {
        "id": "5",
        "name": "test",
        "page": "home_page",
        "content": "this is a test",
        "date_to": "0",
        "item_id": "0",
        "visible": "0",
        "location": "top",
        "date_from": "0",
        "date_type": "always",
        "date_created": "1449168936"
      }
    }
```
  
## Update a Banner

Updates an existing banner. Your request may update any of the properties that are writeable for the Create (POST) operation.

PUT /stores/{store_hash}/v2/banners

PUT /api/v2/banners

### Read-only Properties

The following properties of the banner are read-only. If one or more of these properties are included in the request, it will be rejected.

### Response

Example JSON returned in the response:

```
    {
      "banner": {
        "id": "4",
        "name": "Hi, Jason",
        "page": "home_page",
        "content": "Swanky",
        "date_to": "0",
        "item_id": "0",
        "visible": "0",
        "location": "top",
        "date_from": "0",
        "date_type": "always",
        "date_created": "1441858037"
      }
    }
```
  
## Delete a Banner

Deletes a specified banner. (If successful, this will typically return a "204 No Content".)

DELETE /stores/{store_hash}/v2/banners/{id}

DELETE /api/v2/banners/{id}

## Delete All Banners

Deletes all banners associated with the store. (If successful, this will typically return a "204 No Content".)

DELETE /stores/{store_hash}/v2/banners

DELETE /api/v2/banners

[1]: /api/objects/v2/banner
