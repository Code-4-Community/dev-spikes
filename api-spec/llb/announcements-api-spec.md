# LLB Announcements API Specification

Any routes with the `authorized` path are authorized routes and require a valid JWT.

Any response of `401 UNAUTHORIZED` indicates that a user does not have a valid access token JWT header.

**All routes are prepended with `api/v1/` unless otherwise noted.**

## `GET /announcements`

Gets a list of site-wide announcements. The announcements will be returned in chronological order, from newest to oldest.

### Query Params

##### start: DATE-STRING

The beginning date of when to get announcements from. All returned announcements are created
ON or after the given date string that is given in mm/dd/yyyy format, e.g. 01/19/2020.

##### end: DATE-STRING

The end date of when to get announcements from. All returned announcements will happen ON or before the
given date string that is given in mm/dd/yyyy format, e.g. 12/03/2009.

##### count: INTEGER

The maximum number of announcements to return. The route will return AT MOST count number of announcements.

### Responses

#### `200 OK`

The announcements were retrieved successfully.

```json
{
  "announcements": [
    {
      "id": ID,
      "title": STRING,
      "description": STRING,
      "created": TIMESTAMP
    },
    ...
  ],
  "totalCount": INTEGER
}
```

## `POST /announcements`

Creates a new announcement. Can only be accessed by admins.

### Request

Body:

```json
{
    "title": STRING,
    "description": STRING
}
```

### Responses

#### `200 OK`

The announcement was created successfully.

Body:

```json
{
    "status": "OK"
}
```

#### `400 Bad Request`

Malformed request.

#### `401 Unauthorized`

The user does not have a valid access token JWT header.
