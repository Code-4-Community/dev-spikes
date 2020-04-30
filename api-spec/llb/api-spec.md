# LLB API Specification

Any routes with the `protected` path are routes and require a valid JWT token. This token must be placed in the header with the name "access_token".

Any response of `401 UNAUTHORIZED` with the following body indicates that the user's access_token is expired. In this case the client should initiate a new login, or call the `refresh` route with a valid refresh_token JWT to recieve a new access_token.

```json
"Given access_token is expired"
```

# Table Of Contents
- [Getting Events](#getting-events)
  * [`GET api/v1/protected/events/qualified`](#-get-api-v1-protected-events-qualified-)
  * [`GET api/v1/protected/events/signed_up`](#-get-api-v1-protected-events-signed-up-)
  * [`GET api/v1/protected/events?ids=1,2,3,...`](#-get-api-v1-protected-events-ids-1-2-3--)
- [Single Event](#single-event)
  * [`GET api/v1/protected/events/:event_id`](#-get-api-v1-protected-events--event-id-)
  * [`POST api/v1/protected/events/`](#-post-api-v1-protected-events--)
- [Account Creation Flow](#account-creation-flow)
  * [`POST api/v1/protected/requests`](#-post-api-v1-protected-requests-)
  * [`GET api/v1/protected/requests`](#-get-api-v1-protected-requests-)
  * [`POST api/v1/protected/requests/:request_id/approve`](#-post-api-v1-protected-requests--request-id-approve-)
  * [`POST api/v1/protected/requests/:request_id/reject`](#-post-api-v1-protected-requests--request-id-reject-)
  * [`GET api/v1/protected/requests/:request_id`](#-get-api-v1-protected-requests--request-id-)
- [Site Announcements](#site-announcements)
  * [`GET api/v1/protected/announcements`](#-get-api-v1-protected-announcements-)
  * [`GET api/v1/protected/announcements/:event_id`](#-get-api-v1-protected-announcements--event-id-)
  * [`POST api/v1/protected/announcements`](#-post-api-v1-protected-announcements-)
  * [`POST api/v1/protected/announcements/:event_id`](#-post-api-v1-protected-announcements--event-id-)
  * [`DELETE /api/v1/protected/announcements/:announcement_id`](#-delete--api-v1-protected-announcements--announcement-id-)
- [Event Checkout and Registration](#checkout-registration)
  * [`POST api/v1/protected/checkout/session`](#-post-api-v1-protected-checkout-session)
  * [`POST api/v1/protected/checkout/event`](#-post-api-v1-protected-checkout-event)
  * [`POST api/v1/webhooks/stripe`](#-post-api-v1-webhooks-stripe)



# Getting Events

## `GET api/v1/protected/events/qualified`

Get a list of events that the calling user is qualified for. This includes events a user is already signed up for.

For General Public: Any event happening in the next 5 days are eligible.
For Participating Families: Any event happening in the future.
For Admins: Any  event happening in the future.

## `GET api/v1/protected/events/signed_up`

Get all of the events happening in the future that the given user is signed up for.

##### qualified and signed_up have the same query parameters and responses


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


## `GET api/v1/protected/events?ids=1,2,3,...`

Get the event bodies for the events with the ids that are specified in the query parameter.

### Query Params

##### ids: INT-LIST

The event ids that are being requested. Ids are numbers and they should be seperated by a single comma. If this query parameter is missing, this route returns an empty list. Ignore duplicated values. Ignore ids that do not correspond to any existing event.

### Responses

Equivelent to above routes:

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


# Single Event

## `GET api/v1/protected/events/:event_id`

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
  "capacity": INT,
  "thumbnail": URL,
  "details": {
    "description": STRING,
    "location": STRING,
    "start": TIMESTAMP,
    "end": TIMESTAMP
  }
}
```

## `POST api/v1/protected/events/`

Create a new event. This route must be called by an admin.

### Request Body

```json
{
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

There is an open question on how thumbnails should be handled.

### Responses

#### `200 OK`

The event was created successfully. Will return the event that was just created (including the generated id).

```json
{
  "id": ID,
  "title": STRING,
  "spotsAvailable": INT,
  "capacity": INT,
  "thumbnail": URL,
  "details": {
    "description": STRING,
    "location": STRING,
    "start": TIMESTAMP,
    "end": TIMESTAMP
  }
}
```

#### `401 Unauthorized`

```json
The calling user does not have the required privilege level
```

If the calling user is not an admin.




# Account Creation Flow

## `POST api/v1/protected/requests`

Making a request to be upgraded from GP to PF.
Must be made by a GP user.
Potentially adding bottle nose to prevent multiple denied requests.
Allow multiple outstanding requests?

### Request Body
```json
{
  "description": "STRING"
}
```

### Responses
#### `200 OK`
#### `429 TOO MANY REQUESTS`

## `GET api/v1/protected/requests`

Getting all active requests.
Must be called by an admin.

### Responses

#### `200 OK`
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

#### `401 Unauthorized`

```json
The calling user does not have the required privilege level
```

If the calling user is not an admin.

## `POST api/v1/protected/requests/:request_id/approve`
## `POST api/v1/protected/requests/:request_id/reject`

Approving or rejecting a request by a user to become a PF.
Must be called by an admin.

### Path Params

#### `requst_id`

The id of the request that is being modified.

### Responses
#### `200 OK`

#### `401 Unauthorized`

```json
The calling user does not have the required privilege level
```

If the calling user is not an admin.


## `GET api/v1/protected/requests/:request_id`

Getting the status of a particular request, tbd, approved, or rejected. The user making this call must be either an admin or be the user that initiated this request.

This route may be changed to not require a request_id and instead just get reqest statues based on the user's credentials.

### Responses

#### `200 OK`
```json
{
  "status": "APPROVED" | "REJECTED" | "PENDING"
}
```

#### `401 Unauthorized`
```json
The resource <request_id> is not owned by the calling user and is thus not accessible
```

If the user is not an admin and is requesting the status of a request they did not make.






# Site Announcements

**NOTE: Site-wide and event specific announcements have uniquely identifying ID numbers.**

## `GET api/v1/protected/announcements`

Gets a list of site-wide announcements. The announcements will be returned in chronological order, from newest to oldest.

### Query Params

##### start: DATE-STRING

The beginning date of when to get announcements from. All returned announcements are created ON or after the given date string that is given in mm/dd/yyyy format, e.g. 01/19/2020. Defaults to 3 weeks from `end` or the current date.

##### end: DATE-STRING

The end date of when to get announcements from. All returned announcements will happen ON or before the given date string that is given in mm/dd/yyyy format, e.g. 12/03/2009. Defaults to the current date.

##### count: INTEGER

The maximum number of announcements to return. The route will return AT MOST count number of announcements. Defaults to 50 or all events that exist.

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


## `GET api/v1/protected/announcements/:event_id`

Get any announcements that are specific to a particular event. Events can have multiple announcements associated with them. Announcements should be returned in order of how recent they were created.

### Path Params

#### `event_id`

The ID of the event whose announcements are being queried

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
      "created": TIMESTAMP,
      "event_id": ID
    },
    ...
  ],
  "totalCount": INTEGER
}
```


## `POST api/v1/protected/announcements`

Creates a new site-wide announcement. Can only be accessed by admins.

### Request

Body:

```json
{
    "announcement": {
        "title": STRING,
        "description": STRING
    }
}
```

### Responses

#### `200 OK`

The announcement was created successfully.

```json
{
  "id": ID,
  "title": STRING,
  "description": STRING,
  "created": TIMESTAMP
}
```

#### `401 Unauthorized`

```json
The calling user does not have the required privilege level
```

If the calling user is not an admin.

## `POST api/v1/protected/announcements/:event_id`

Creates a new announcement for the given event. Can only be accessed by admins.

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

```json
{
  "id": ID,
  "title": STRING,
  "description": STRING,
  "created": TIMESTAMP,
  "event_id": ID
}
```

#### `401 Unauthorized`

```json
The calling user does not have the required privilege level
```

If the calling user is not an admin.


## `DELETE /api/v1/protected/announcements/:announcement_id`

Deletes an announcment with the specified announcment id. This route can be used to delete site-wide or event specific because they have unique announcement identifiers. This route can only be called by an admin.

### Path Params

#### `announcment_id`

The ID of the announcement being deleted.

### Requests

#### `200 OK`

The announcement was deleted successfully.

#### `401 Unauthorized`

```json
The calling user does not have the required privilege level
```

If the calling user is not an admin.





# Event Checkout and Registration  

The events in a user's cart are stored in Vuex on the front end.

## `POST api/v1/protected/checkout/session`

Buying and checking out a list of events, with Stripe

### Request Body

```
const body = {
  lineItems: cartEvents.map(event => ({
    id: event.id,
    name: event.title,
    description: event.details.description,
    amount: 5,
    currency: 'usd',
    quantity: 1,
  })),
  successUrl: 'http://localhost:8080/?session_id={CHECKOUT_SESSION_ID}',
  cancelUrl: 'http://localhost:8080/checkout',
};
```


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
