# Login and Token Endpoints

Common Headers: 
```json
  Content-Type: application/json
```

Access Token Composition:

``` json
{
    "userId": UUID,
    "adminLevel": INT,
    "accountStatus": INT,
    "expiration": TIMESTAMP, 
}

```
- TIMESTAMP: time in milliseconds in UTC.
- adminLevel: integer describing the user's priviledge level. 
    - 0: Not an admin.
    - 1: Is an admin.
    - 2: LLB Terminal Illness family? // TODO
- accountStatus: integer describing the status of the account.
    - 0: Account not yet confirmed.
    - 1: Account confirmed.
    - 2: Account awaiting admin confirmation.


## `POST user/login`

>Used for logging in.

### __Request__

Body:
```json
{
    "username" : EMAIL or USERNAME,
    "password" : STRING,
    "rememberMe": BOOLEAN
}
```
  An EMAIL is a string representing a user's email.

  A USERNAME is a string representing a user's username.
  
### __Responses__ 
 

#### `201 Created`

> The username/password combination is valid. Includes distinct access and refresh tokens for the given user. 

Body: 
```json
{
  "accessToken"  : JWT,
  "refreshToken" : JWT
}
```
#### `400 Bad Request`
> Malformed request.

#### `401 Unauthorized`
> The username/password combination is invalid.

## `POST user/login/refresh`

> Used for getting a new access token.

### __Request__

Headers:
- ***refreshToken : JWT***

### __Responses__ 

#### `201 Created`
> The refresh token is valid and has not yet expired. Response includes a new unique access_token.

Body: 
```json
{
  "accessToken" : JWT,
}
```

#### `401 Unauthorized`
> The refresh token is invalid.


## `DELETE user/login`

> Used for logging out.

### __Request__

Headers:
- ***refreshToken : JWT***

### __Responses__ 

#### `204 No Content`
> Logout successful.


## `POST user/signup`

> Used for signing up a new user.

Body

```json
{
  "email" : EMAIL,
  "username": STRING,
  "password" : PASSWORD,
  "firstName" : STRING,
  "lastName" : STRING
}
```

- EMAIL: Is a valid email string.
- PASSWORD: Is a valid password string that is at least 8 characters.

### __Responses__ 

#### `201 Created`
> The username and email are still available, and an account has been successfully created.

Body: 
```json
{
  "accessToken"  : STRING,
  "refreshToken" : STRING
}
```
 
#### `400 Bad Request`
> Malformed request body.

```jsonld=
{
    "error_message": STRING
}
```

#### `409 Conflict`
> There has been a conflict regarding the unique identifiers email and/or username.
