# eegeo/eegeo-routing-service-api

This respository contains documentation and examples to help you use eeGeo's routing service.  

Routes are calculated using path information provided through the [indoor maps API](https://github.com/eegeo/eegeo-indoor-maps-api).
Routes will be available for buildings where:
* Path information was provided for the building through the indoor maps API.
* The building submission has been approved.
* The approved building has been promoted to the public manifest.

Only routes within a single building are supported.

Routes are calculated using a fork of the [Open Source Routing Machine (OSRM)](https://github.com/eegeo/osrm-backend). Minor changes have been made to support the information required to describe indoor routes.  

## HTTP endpoints

Routes can be obtained from two service endpoints using HTTP GET requests. Only the following endpoints are available; the eeGeo routing service does not support general OSRM requests. Responses from both endpoints use the same JSON format, following that of OSRM routes which include steps and annotations. The route geometry is specified as geoJSON. 

The top level JSON attributes in a response are:

|Attribute|Type|Description|
 --- | --- | --- 
|`code`| string | "Ok" if the request was successful; a high level error message otherwise.
|`routes` | array of [route](https://github.com/eegeo/osrm-backend/blob/master/docs/http.md#route) JSON objects | Descriptions of each route found.
|`waypoints`| array of [waypoint](https://github.com/eegeo/osrm-backend/blob/master/docs/http.md#waypoint) JSON objects | Significant locations along the route.
|`error` | string | Detailed error information if the request was not successful.

Multiple routes may be returned, but there is no guarantee that all routes will be found.

Note that the given location points will be resolved to points specified in the path information for the indoor map.  Because of this, the route may not start and end precisely at the specified points.

### route 

The basic route query finds the shortest path between the given coordinates.  This endpoint is suitable when only one floor of the building has path information.

#### Example
 
```https://routing.eegeo.com/v1/route/?loc=-1.910638,52.468141%3B-1.926356,52.467736&apikey=<apikey>```


|Parameter|Description|
 --- | --- 
|`loc`   | A semicolon separated list of points expressed as longitude,latitude.  Note that the semicolon may be encoded as %3B.
|`apikey`| An eeGeo application API token, obtained from the [eeGeo developer site](http://www.eegeo.com/developers/apikeys/).


### routelevels

The routelevels query finds a path between the given coordinates, using the level information to determine start and end floor levels within the building.


#### Example

```https://routing.eegeo.com/v1/routelevels/?loc=-2.9786783981,56.4602716589%3B-2.97831166606,56.4600344411&levels=0%3B2&apikey=<apikey>```

|Parameter|Description|
 --- | --- 
|`apikey`| An eeGeo application API token, obtained from the [eeGeo developer site](http://www.eegeo.com/developers/apikeys/).
|`loc`   | A semicolon separated list of points expressed as longitude,latitude.  Note that the semicolon may be encoded as %3B.
|`levels`| A semicolon separated list of levels, one per location.
|`limit`| Optional maximum distance in meters from loc points to the route start/end point. Default value is 10.




