# Calling API Routes
## Example: Create Event (LLB)
### Using Postman
#####Login 
Use postman to get a handle of how the endpoint should work before you start coding it. 
1. Download at https://www.postman.com/
2. Create a new collection called "LLB"
3. Hit the 3 dots as you hover over the collection and create a "New Request"
4. For our case, we want to make a request called "Login" which is post route.
5. Make this the url of the request: http://lucy.c4cneu.com/api/v1/user/login
6. Hit the body section and the body of the request in:
```json
{
	"email": "test@gmail.com",
	"password": "test"
}
```
7. Select Raw as the option for the body.
8. Hit the blue "send" button
9. you should get a response that looks like this
    {
        "accessToken": "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJwcml2aWxlZ2VMZXZlbCI6MiwiaXNzIjoiYzRjIiwiZXhwIjoxNTg2NzMzNzkwLCJ1c2VySWQiOjd9.mqcIs4iRduXAXxu40IepjrMnc1ZOJlZUHyr_edOIcqo",
        "refreshToken": "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJwcml2aWxlZ2VMZXZlbCI6MiwiaXNzIjoiYzRjIiwiZXhwIjoxNTg3MzM2NzkwLCJ1c2VySWQiOjd9.CzOSnbhjeGYZCXrMWtgoH9y3Xrs_uUUNOucmWfZGD_0"
    }

#####Create Event
1. Create a new Request
2. Make another Post named "create event"
3. Use this url: http://lucy.c4cneu.com/api/v1/protected/events/
4. Use this as the body: 
    {
    	"title":"afsasf",
    	"spotsAvailable":10,
    	"thumbnail":"https://www.mysqltutorial.org/mysql-timestamp.com",
    	"details": {
    		"description":"asfaf",
    		"location":"location stub",
    		"start": 62072330338,
    		"end": 62072329438
    	}
    }
5. Since this is a protected route, we need to pass in the jwt values to access it.
6. Go to the headers section by body, add keys X-Access-Token and X-Refresh-Token
7. The values should be whatever the repsonse was from step 9 of the login.
8. Hit send, if you don't get an error back, you've formatted the body correctly.

###Integrating the call into the Frontend
###### In src/api/api.js

    async function createEvent(event) {
      const body = {
        title: event.name,
        spotsAvailable: 10,
        thumbnail: events.img,
        details: {
          description: event.description,
          location: event.location,
          start: formatTimestamp(event.date, event.startTime),
          end: formatTimestamp(event.date, event.endTime),
        },
      };
    
      let res;
      try {
        res = await protectedResourceAxios.post(`${baseUrl}/protected/events/`, body);
      } catch (err) {
        return err;
      }
    
      return res;
    }``

###### Whats happening here?
1. First, we declare the call as an async function because querying the api is asyncronous.
2. Since creating an event is a post call, we format the body base off of what the api spec calls for.
3. Any time you call something asyncronous, put it in a try catch since they are error prone.
4. Then, call the api by putting the route as the first parameter, and the body as the second. Note: nicks protectedResourceAxios and publicResourceAxios handle the headers for us. If you are calling a public route, use publicResourceAxios instead. This is already imported into the api.js file.
5. Return the response.

###### Import the function where you need it
In the script part of the vue template, "import api from '../api/api';"
Our form calls the onSubmit function when the. form is supposed to be saved to the database.
    
        onSubmit() {
          this.$validator.validateAll().then((result) => {
            if (result) {
              try {
                api.createEvent(this.event);
              } catch (err) {
                this.error = err;
              }
            }
          });
        },
     
###### What happened here?
1. We imported the api module from above.
2. This gives us access to the createEvent function as shown in line 5.
3. We then pass in the event that we want to save.
3. We catch any errors so that we can debug.

### Debugging
For debugging always make sure that you have the networks tab opened in your browsers console.
The two main places you will want to look are your headers view within networks and the response view. 
1. The headers view will tell you what the request payload was so that you can verify that you sent what you intended..
2. The response will tell you what the api sent back and what error may have happened.

### Possible Errors
- 404 Errors
	If you get a 404, verify that you entered the correct url. 99% of the time you mispelled something or had something like "/protected//createEvent"
	If you're sure that you're url is right, ask in slack if the route is working.
- Authentication Errors (401 or something related to auth in the response)
	Verify that you are logged in as a user in the browser.
	Make sure that your user is an admin if the route requires admin permissions.
	Ask Ben, Liam, or Nic if you're still stuck
- Bad Format
	Verify that you spelled every field correctly of the object you're passing in
	Verify that every type is correct (ex. not passing in a number when it should be a string)
	Ask someone for a working example in Postman, if you're still stuck so that you can compare bodies.


