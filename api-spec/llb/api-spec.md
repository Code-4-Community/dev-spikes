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


# Starred Stuff

## `POST api/v1/authorized/users/:user_id/starred`

Adding a single event to a user's starred list.

### Request Body

```jsonld=
{
    "event_id": EVENT_ID
}
```

### Responses

#### `200 OK`

The event was added successfully.

#### `400 OK`

The event id was not a valid event for the user.

```jsonld=
{
    "error_message": STRING
}
```

#### `401 UNAUTHORIZED`


## `DELETE api/v1/authorized/users/:user_id/starred`

Deleting an event from a user's starred list.

This routes has identical request and responses as the `POST api/v1/users/:user_id/starred` route except it will not check the validity of an event id to throw a `400`.
