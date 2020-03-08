# LLB API Specification

Any routes with the `authorized` path are authorized routes and require a valid JWT.

Any response of `401 UNAUTHORIZED` indicates that a user does not have a valid access token JWT header.

## `GET api/v1/events`
### `GET api/v1/authorized/users/:user_id/events/signed_up`
### `GET api/v1/authorized/users/:user_id/events/qualified`
### `GET api/v1/authorized/users/:user_id/starred`

Getting a list of events. Either all events, all events a user is signed up for, all events
a user is qualified for, or all events a user has stared as wanting to attend.

### Query Params

##### start: DATE-STRING 

The beginning date of when to get events from. All returned events will happen ON or after
the given date string that is given in mm/dd/yyyy format, ie: 01/19/2020

##### end: DATE-STRING

The latest date of when to get events from. All returned events will happen ON or before the
given date string that is given in mm/dd/yyyy format, ie: 12/03/2009

##### count: INTEGER

The maximum number of events to return. The route will return AT MOST count number of events.

### Responses

#### `200 OK`

The events were sent successfully.

```json
{
  "events": [
    {
      "id": ID,
      "title": STRING,
      "spotsAvailable": INT,
      "thumbnail": URL,
      "details": {
        "description": STRING,
        "location": STRING,
        "start": TIMESTAMP,
        "end": TIMESTAMP
      }
    },
    ...
  ],
  "totalCount": INTEGER
}
```



## `GET api/v1/events/:event_id`

Gets a specific event with the specified id.

### Route Params

##### event_id: ID

The id number of the event that is being queried.

### Responses

#### `200 OK`

The event was retrieved successfully.

```json
{
  "id": ID,
  "title": STRING,
  "spotsAvailable": INT,
  "thumbnail": URL,
  "details": {
    "description": STRING,
    "location": STRING,
    "start": TIMESTAMP,
    "end": TIMESTAMP
  }
}
```


# Account Creation Flow

## `POST api/v1/requests`

Making a request to be upgraded from GP to PF.
Must be made by a GP user.
Potentially adding bottle nose to prevent multiple denied requests.
Allow multiple outstanding requests?

Request
```json
{
  "description": "STRING"
}
```

Response
`200 OK`
`429 TOO MANY REQUESTS`

## `GET api/v1/requests`

Getting all active requests.
Must be called by an admin.

Response
```json
{
  "requests": [
    {
      "id": "request_id STRING",
      "description": "STRING",
      "user": "user's name STRING"
    },
    ...
  ]
}
```

## `POST api/v1/requests/:request_id/approve`
## `POST api/v1/requests/:request_id/reject`

Approving or rejecting a request by a user to become a PF.
Must be called by an admin.
Always `200 OK`.


## `GET api/v1/requests/:request_id`

Getting the status of a particular request, tbd, approved, or rejected

Response
```json
{
  "status": "APPROVED" | "REJECTED" | "PENDING"
}
```







# Cart Stuff

The events in a user's cart are stored in local storage on the front end.

## `POST api/v1/authorized/users/:user_id/checkout`

Buying and checking out a list of events.

### Request Body

```json=
{
    "events": [
        {
            "id": EVENT_ID,
            "tickets": INTEGER
        },
        ...
    ]
}
```

- EVENT_ID: A UUID object corresponding to an event the user is trying to sign up for.

### Responses

#### `200 OK`

The purchase was successful.

#### `400 BAD REQUEST`

One of the event ids are not valid events for the given user.

```jsonld=
{
    "error_message": STRING
}
```

#### `401 UNAUTHORIZED`
