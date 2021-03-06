# Schemas

Schemas are rooted in the `/v1/schemas/{org_label}/{project_label}` collection.

Each schema... 

- belongs to a `project` identifier by the label `{project_label}` 
- inside an `organization` identifier by the label `{org_label}` 
- it is validated against the [shacl schema](https://bluebrain.github.io/nexus/schemas/shacl-20170720.ttl) (version 20170720).

Access to resources in the system depends on the access control list set for them. Depending on the access control list, a caller may need to prove its identity by means of an **access token** passed to the `Authorization` header (`Authorization: Bearer {token}`). Please visit @ref:[Authentication](../iam/authentication.md) to learn more about how to retrieve an access token.

@@@ note { .tip title="Running examples with Postman" }

The simplest way to explore our API is using [Postman](https://www.getpostman.com/apps). Once downloaded, import the [schemas collection](../assets/schema-postman.json).

If your deployment is protected by an access token: 

Edit the imported collection -> Click on the `Authorization` tab -> Fill the token field.

@@@

## Create a schema using POST

```
POST /v1/schemas/{org_label}/{project_label}
  {...}
```

The json payload: 

- If the `@id` value is found on the payload, this @id will be used.
- If the `@id` value is not found on the payload, an @id will be generated as follows: `base:{UUID}`. The `base` is the `prefix` defined on the resource's project (`{project_label}`).

**Example**

Request
:   @@snip [schema.sh](../assets/schema.sh)

Payload
:   @@snip [schema.json](../assets/schema.json)

Response
:   @@snip [schema-ref-new.json](../assets/schema-ref-new.json)


## Create a schema using PUT
This alternative endpoint to create a schema is useful in case the json payload does not contain an `@id` but you want to specify one. The @id will be specified in the last segment of the endpoint URI.
```
PUT /v1/schemas/{org_label}/{project_label}/{schema_id}
  {...}
```
 
Note that if the payload contains an @id different from the `{schema_id}`, the request will fail.

**Example**

Request
:   @@snip [schema-put.sh](../assets/schema-put.sh)

Payload
:   @@snip [schema.json](../assets/schema.json)

Response
:   @@snip [schema-ref-new.json](../assets/schema-ref-new.json)


## Update a schema

This operation overrides the payload.

In order to ensure a client does not perform any changes to a resource without having had seen the previous revision of
the resource, the last revision needs to be passed as a query parameter.

```
PUT /v1/schemas/{org_label}/{project_label}/{schema_id}?rev={previous_rev}
  {...}
```
... where `{previous_rev}` is the last known revision number for the schema.


**Example**

Request
:   @@snip [schema-update.sh](../assets/schema-update.sh)

Payload
:   @@snip [schema.json](../assets/schema.json)

Response
:   @@snip [schema-ref-new-updated.json](../assets/schema-ref-new-updated.json)


## Tag a schema

Links a schema revision to a specific name. 

Tagging a schema is considered to be an update as well.

```
PUT /v1/schemas/{org_label}/{project_label}/{schema_id}/tags?rev={previous_rev}
  {
    "tag": "{name}",
    "rev": {rev}
  }
```
... where 

- `{previous_rev}`: Number - is the last known revision for the resolver.
- `{name}`: String - label given to the schemas at specific revision.
- `{rev}`: Number - the revision to link the provided `{name}`.

**Example**

Request
:   @@snip [schema-tag.sh](../assets/schema-tag.sh)

Payload
:   @@snip [tag.json](../assets/tag.json)

Response
:   @@snip [schema-ref-new-tagged.json](../assets/schema-ref-new-tagged.json)

## Add attachment to a schema

Adds a binary to an already existing schema

```
PUT /v1/schemas/{org_label}/{project_label}/{schema_id}/atachments/{name}?rev={previous_rev}
```
...where

- `{previous_rev}`: is the last known revision number for the schema.
- `{name}`: String - the attachment identifier. This value is uniquely identifying the attachment per project.

**Example**

Request
:   @@snip [schema-attach.sh](../assets/schema-attach.sh)

Response
:   @@snip [schema-ref-new-attached.json](../assets/schema-ref-new-attached.json)

## Delete attachment from a schema

Deletes the attachment metadata from the latest revision of the schema. The attachment will still be accessible accessing the previous revision.

```
DELETE /v1/schemas/{org_label}/{project_label}/{schema_id}/atachments/{name}?rev={previous_rev}
```
...where

- `{previous_rev}`: is the last known revision number for the schema.
- `{name}`: String - the attachment identifier. This value is uniquely identifying the attachment per project.

**Example**

Request
:   @@snip [schema-unattach.sh](../assets/schema-unattach.sh)

Response
:   @@snip [schema-ref-new-unattached.json](../assets/schema-ref-new-unattached.json)

## Fetch attachment from a schema (current revision)

```
GET /v1/schemas/{org_label}/{project_label}/{schema_id}/atachments/{name}
```
...where `{name}` is the attachment identifier.

**Example**

Request
:   @@snip [schema-attach-fetch.sh](../assets/schema-attach-fetch.sh)

## Fetch attachment from a schema (specific revision)

```
GET /v1/schemas/{org_label}/{project_label}/{schema_id}/atachments/{name}?rev={rev}
```
... where 
- `{name}` - String:  is the attachment identifier.
- `{rev}` - Long: is the revision number of the resource to be retrieved.

**Example**

Request
:   @@snip [schema-attach-fetch-rev.sh](../assets/schema-attach-fetch-rev.sh)

## Fetch attachment from a schema (specific tag)

```
GET /v1/schemas/{org_label}/{project_label}/{schema_id}/atachments/{name}?tag={tag}
```
... where 
- `{name}` - String:  is the attachment identifier.
- `{tag}` - String: is the tag of the schema to be retrieved.

**Example**

Request
:   @@snip [schema-attach-fetch-tag.sh](../assets/schema-attach-fetch-tag.sh)

## Deprecate a schema

Locks the schema, so no further operations can be performed. It also deletes the schema from listing/querying results.

Deprecating a schema is considered to be an update as well. 

```
DELETE /v1/schemas/{org_label}/{project_label}/{schema_id}?rev={previous_rev}
```

... where `{previous_rev}` is the last known revision number for the schema.

**Example**

Request
:   @@snip [schema-deprecate.sh](../assets/schema-deprecate.sh)

Response
:   @@snip [schema-ref-new-deprecated.json](../assets/schema-ref-new-deprecated.json)


## Fetch a schema (current version)

```
GET /v1/schemas/{org_label}/{project_label}/{schema_id}
```

**Example**

Request
:   @@snip [schema-fetch.sh](../assets/schema-fetch.sh)

Response
:   @@snip [schema-fetched.json](../assets/schema-fetched.json)


## Fetch a schema (specific version)

```
GET /v1/schemas/{org_label}/{project_label}/{schema_id}?rev={rev}
```
... where `{rev}` is the revision number of the schema to be retrieved.

**Example**

Request
:   @@snip [schema-fetch-revision.sh](../assets/schema-fetch-revision.sh)

Response
:   @@snip [schema-fetched.json](../assets/schema-fetched.json)


## Fetch a schema (specific tag)

```
GET /v1/schemas/{org_label}/{project_label}/{schema_id}?tag={tag}
```

... where `{tag}` is the tag of the resource to be retrieved.


**Example**

Request
:   @@snip [schema-fetch-tag.sh](../assets/schema-fetch-tag.sh)

Response
:   @@snip [schema-fetched-tag.json](../assets/schema-fetched-tag.json)


## List schemas

```
GET /v1/schemas/{org_label}/{project_label}?from={from}&size={size}&deprecated={deprecated}&q={full_text_search_query}
```

where...

- `{full_text_search_query}`: String - can be provided to select only the schemas in the collection that have attribute values matching (containing) the provided token; when this field is provided the results will also include score values for each result
- `{from}`: Number - is the parameter that describes the offset for the current query; defaults to `0`
- `{size}`: Number - is the parameter that limits the number of results; defaults to `20`
- `{deprecated}`: Boolean - can be used to filter the resulting schemas based on their deprecation status


**Example**

Request
:   @@snip [schema-list.sh](../assets/schema-list.sh)

Response
:   @@snip [schema-list.json](../assets/schema-list.json)