# ArcGis Requests for SFTT
###### Gabriel Peter: February 2, 2020

ArcGis is generally a paid software from ESRI; however, we can [apply](https://www.esri.com/en-us/solutions/industries/sustainability/nonprofit-program/overview) for nonprofit organization access

## Schema Overview
- Required: FID, GlobalID, CreationDate ...
- Optional: ST_NAME(string), ST_TYPE(string), STREET_ID(string), RESERVED([0-1]), RESERVED_DATE(date)
    - We should agree upon these and think about how/when to connect this data to our current backend
- Columns/Fields can be easily added via the [developer console](https://developers.arcgis.com/dashboard?message=sign-up-success).
- [Interactive Map Sandbox](https://code4community.maps.arcgis.com/home/webmap/viewer.html) Requires Sign-In -> Ask me for the credentials if needed
- I deleted approximately 30 + attributes with useless junk from the original [shapefile](http://bostonopendata-boston.opendata.arcgis.com/datasets/cfd1740c2e4b49389f47a9ce2dd236cc_8?geometry=-71.129%2C42.333%2C-71.020%2C42.355).


## Requests Overview

- [Postman Examples I made](https://www.getpostman.com/collections/b7ea45fff0055fdc4ea6)
    - Notice that all requests are "POST"
- [ArcGis Interactive Backend For Endpoint (TEST LAYER)](https://services7.arcgis.com/iIw2JoTaLFMnHLgW/ArcGIS/rest/services/boston_street_segments_1/FeatureServer/0)

## Simple Querying 

[Documentation](https://developers.arcgis.com/labs/rest/query-a-feature-layer/)

    var settings = {
        "url": "https://<baseURL>/query",
        "method": "POST",
        "timeout": 0,
        "headers": {
            "Content-Type": "application/x-www-form-urlencoded"
        },
        "data": {
            "f": "json",
            "where": "FID = 878",
            "outSr": "4326",
            "outFields": "*",
            "token": currently not needed but will later be used for production
        }
    };

- where: SQL Expression
- outSr: states the type of geographical data we are using (Don't change)
- outFields: the columns of data that will be returned per filtered row, * givens the complete object.
- token: Peep OAuth token retreival later on.

## Adding/Deleting

[Documentation](https://developers.arcgis.com/labs/rest/add-edit-and-remove-features/)

This should be a disabled feature in production as their is never a reason to delete a street or add one beyond data preprocessing if we needed to clean the dataset before public usage.

    var settings = {
        "url": "https://<baseURL>/applyEdits",
        "method": "POST",
        "timeout": 0,
        "headers": {
            "Content-Type": "application/x-www-form-urlencoded"
        },
        "data": {
            "f": "json",
            "deletes": "[1, 3535, 10041]",
            "adds": ...
            "token": ...
        }
    };

- deletes: only accepts "FID"
- adds: submit an entire object following the current schema of the data

## Updating

[Documentation](https://developers.arcgis.com/labs/rest/add-edit-and-remove-features/)


    var settings = {
        "url": "https://services7.arcgis.com/iIw2JoTaLFMnHLgW/ArcGIS/rest/services/boston_street_segments_1/FeatureServer/0/applyEdits",
        "method": "POST",
        "timeout": 0,
        "headers": {
            "Content-Type": "application/x-www-form-urlencoded"
        },
        "data": {
            "f": "json",
            "updates": "[
                {
                    \"attributes\" : {
                    \"FID\" : \"17894\",
                    \"ST_NAME\" : \"EXAMPLE\",
                    \"RESERVED\": \"1\"
                    }
                }
            ]"
        }
    };

- Note sure if the escape keys are always necessary
- When this particular configuration is POSTed, this will successfully update the street segment of FID: 17894 to have a street name of "EXAMPLE" and a reserved status of 1 or true.
- In production things we should change ONLY are fields such as: reserved?, date of reservation, etc.

## Token Retrieval

[Documentation](https://developers.arcgis.com/labs/rest/get-an-access-token/)

    var settings = {
        "url": "https://www.arcgis.com/sharing/rest/oauth2/token",
        "method": "POST",
        "timeout": 0,
        "headers": {
            "Content-Type": "application/x-www-form-urlencoded"
        },
        "data": {
            "client_id": "I0YjQpduKSwoHave",
            "client_secret": "ac11027fcd904482828636bce3fbe517",
            "grant_type": "client_credentials"
        }
    };

- Client ID/Secret: Both accessible from the console
- More research is needed to be done on renewal and specifying expiration times



