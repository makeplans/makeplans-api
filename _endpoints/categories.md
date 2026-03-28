---
title: Categories
nav_order: 9
---

Services can be listed in a category. Categories can be presented like a tree. Root categories are defined with empty `{parent_id}`.

## Attributes

<table>
  <tr><th>Name</th><th>Type</th><th>Description</th></tr>
  <tr><td>id</td><td>Integer</td><td>Automatically set</td></tr>
  <tr><td>created_at</td><td>Datetime</td><td>Automatically set</td></tr>
  <tr><td>updated_at</td><td>Datetime</td><td>Automatically set</td></tr>
  <tr><td>title</td><td>String</td><td>Required</td></tr>
  <tr><td>parent_id</td><td>Integer</td><td></td></tr>
  <tr><td>custom_data</td><td>Array</td><td>Not required.</td></tr>
  <tr><td>priority_value</td><td>Integer</td><td>Not required. Priority value for ordering.</td></tr>
</table>

## Listing

`GET /categories` will return all categories.

Response

```json
[
  {
    "category": {
      "created_at": "2012-09-20T15:34:16+02:00",
      "custom_data": null,
      "id": 1,
      "parent_id": null,
      "title": "My first little category",
      "updated_at": "2012-09-20T15:34:16+02:00"
    }
  }
]
```

## Get category

`GET /categories/{category_id}` will get a category with id `{category_id}`.

## Add new category

`POST /categories` will create a new category. To add connected services specify array of `{service_id}` in parameter `{service_id_list}`.

## Update category

`PUT /categories/{category_id}` will update existing category with id `{category_id}`. To add connected services specify array of `{service_id}` in parameter `{service_id_list}`.

## Delete category

`DELETE /categories/{category_id}` will delete existing category with id `{category_id}`.

## List services

`GET /categories/{category_id}/services` will return all services for category with id `{category_id}`.

For any additional usage of a service please use the main [service endpoint](/endpoints/services/).
