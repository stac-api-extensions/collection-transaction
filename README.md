# STAC API - Collection Transaction Extension <!-- omit in toc -->

- [Overview](#overview)
- [Methods](#methods)
  - [POST](#post)
  - [PUT](#put)
  - [PATCH](#patch)
  - [DELETE](#delete)

## Overview

- **Title:** Collection Transaction
- **OpenAPI specification:** [openapi.yaml](openapi.yaml)
- **Conformance URIs:**
  - <https://api.stacspec.org/v1.0.0/collections/extensions/transaction>
- **Scope:** STAC API - Collections
- **[Extension Maturity Classification](https://github.com/radiantearth/stac-api-spec/tree/main/README.md#maturity-classification):** Candidate
- **Dependencies**:
  - [STAC API - Collections](https://github.com/radiantearth/stac-api-spec/tree/v1.0.0/ogcapi-features/README.md)
- **Owner**: @m-mohr

The STAC API Collection Transaction Extension adds support for the creation, modification, and deletion
of collections through POST, PUT, PATCH, and DELETE method requests.

This extension is closely aligned with the [Transaction Extension for Items](https://github.com/stac-api-extensions/transaction).
If anything is unclear, the implementations check the Transaction Extension for Items for clarification.

## Methods

| Path                                 | Content-Type Header | Body                                              | Success Status | Description                                                             |
| ------------------------------------ | ------------------- | ------------------------------------------------- | -------------- | ----------------------------------------------------------------------- |
| `POST /collections`                  | `application/json`  | partial Collection or partial list of Collections | 201, 202       | Adds a new collection to a server.                                      |
| `PUT /collections/{collectionId}`    | `application/json`  | partial Collection                                | 200, 202, 204  | Updates an existing item by ID using a complete Collection description. |
| `PATCH /collections/{collectionId}`  | `application/json`  | partial Collection                                | 200, 202, 204  | Updates an existing item by ID using a partial Collection description.  |
| `DELETE /collections/{collectionId}` | n/a                 | n/a                                               | 200, 202, 204  | Deletes an existing Collection by ID.                                   |

### POST

When the body is a partial Collection:

- Must only create a new resource.
- Must have an `id` field.
- Must return 409 if a Collection exists for the same `id` field values.
- Must return 201 and a `Location` header with the URI of the newly added resource for a successful operation.
- May return the content of the newly added resource for a successful operation.

When the body is a partial list of Collections:

- Must only create a new resource.
- Each Item in the list of Collections must have an `id` field.
- Must return 409 if a Collection exists for any of the id values.
- Must return 201 without a `Location` header.
- May create only some of the Collections in the list of Collections. Implementations are not
  required to implement all-or-none sematics for this operation. For example, if a list of Collections
  contains two Collections and one is successfully created and the other
  fails to be created, the server is not required to then delete the successfully
  created one. When only some of the Collections are created, the
  server should communicate this failure back to the client with an error status code.

All cases:

- Must return 202 if the operation is queued for asynchronous execution.

### PUT

- Must populate the `id` field in the Collection from the URI.
- Must return 200 or 204 for a successful operation.
- If 200 status code is returned, the server shall return the content of the updated resource for a successful operation.
- Must return 202 if the operation is queued for asynchronous execution.
- Must return 404 if no Collection exists for this resource URI.
- If the `id` fields is different from those in the URI, status code 400 shall be returned.

### PATCH

- Must populate the `id` field in the Collection from the URI.
- Must return 200 or 204 for a successful operation.
- If status code 200 is returned, the server shall return the content of the updated resource for a successful operation.
- May return the content of the updated resource for a successful operation.
- Must return 202 if the operation is queued for asynchronous execution.
- Must return 404 if no Collection exists for this resource URI.
- If the `id` field is different from those in the URI, status code 400 shall be returned.

PATCH is compliant with [RFC 7386](https://tools.ietf.org/html/rfc7386).

### DELETE

- Must return 200 or 204 for a successful operation.
- Must return a 202 if the operation is queued for asynchronous execution.
- May return a 404 if no Collection existed prior to the delete operation. Returning a 200 or 204 is also valid in this situation.
