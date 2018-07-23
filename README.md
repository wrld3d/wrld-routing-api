![WRLD](/images/routing-screenshot-indoor.png)

WRLD Routing REST API v1.0.0
============================

The WRLD routing API allows you to find a route from point A to point B within a building created using the [wrld-indoor-maps-api](https://github.com/wrld3d/wrld-indoor-maps-api).  Routes are available to applications through a REST API at `https://routing.wrld3d.com/`.

This respository contains documentation and examples to help you use WRLD's routing API.  

Routes are calculated using a fork of the [Open Source Routing Machine (OSRM)](https://github.com/wrld3d/osrm-backend), a high performance routing engine designed for use with OpenStreetMap data. We're grateful to the [OSRM project](https://github.com/Project-OSRM) and all of its developers for making this available.  We've made minor changes to support the information required to describe indoor routes.  

## Service HTTP endpoints

Routes can be obtained from two service endpoints using HTTP GET requests. Only the following endpoints are available; the WRLD routing API does not support general OSRM requests. Responses from both endpoints use the same JSON format, following that of OSRM routes which include steps and annotations. The route geometry is specified as geoJSON. 

The top level JSON attributes in a response are:

|Attribute|Type|Description|
 --- | --- | --- 
|`code`| string | "Ok" if the request was successful; a high level error message otherwise.
|`type` | string | "multipart" if the returned route is made up of several smaller routes.
|`routes` | array of route containers | Each of these contains an array of [route](https://github.com/wrld3d/osrm-backend/blob/master/docs/http.md#route) options for each section of the overall journey.
|`waypoints`| array of [waypoint](https://github.com/wrld3d/osrm-backend/blob/master/docs/http.md#waypoint) JSON objects | Significant locations along the route.
|`error` | string | Detailed error information if the request was not successful.

Multiple routes may be returned, but there is no guarantee that all routes will be found.

Note that the given location points will be resolved to points specified in the path information for the indoor map.  Because of this, the route may not start and end precisely at the specified points.

### route 

The basic route query finds the shortest path between the given coordinates.  This endpoint is suitable for both indoor and outdoor routes, or combinations of the two.

#### Example (outdoor)

```
https://routing.wrld3d.com/v1/route?loc=-1.910638,52.468141%3B-1.926356,52.467736&apikey=<apikey>
```

#### Example (indoor to outdoor)

```
https://routing.wrld3d.com/v1/route?loc=-2.983122836389253,56.46123165326403,2%3B-2.9725653,56.4556209&apikey=<apikey>
```

|Parameter|Description|
 --- | --- 
|`loc`   | A semicolon separated list of points expressed as longitude,latitude, with an optional floor level index for waypoints that are indoors.  Note that the semicolon may be encoded as %3B.
|`apikey`| An WRLD application API token, obtained from the [WRLD developer site](http://www.wrld3d.com/developers/apikeys/).
|`limit`| Optional maximum distance in meters from loc points to the route start/end point. Default value is 10.
|`travelmode`| Optional desired transport mode for routing. Default value is "walking". Note: "driving" routes are available for limited areas.  Please contact [support@wrld3d.com](mailto:support@wrld3d.com) for details.

### routelevels

The routelevels query finds a path between the given coordinates, using the level information to determine start and end floor levels within the building.

#### Example

```
https://routing.wrld3d.com/v1/routelevels/?loc=-2.9786783981,56.4602716589%3B-2.97831166606,56.4600344411&levels=0%3B2&apikey=<apikey>
```

|Parameter|Description|
 --- | --- 
|`apikey`| An WRLD application API token, obtained from the [WRLD developer site](http://www.wrld3d.com/developers/apikeys/).
|`loc`   | A semicolon separated list of points expressed as longitude,latitude.  Note that the semicolon may be encoded as %3B.
|`levels`| A semicolon separated list of levels, one per location.
|`limit`| Optional maximum distance in meters from loc points to the route start/end point. Default value is 10.

## Route availability

Routes will be available for buildings where:
* Path information was provided for the building through the indoor maps API.
* The building submission has been approved.
* The approved building has been promoted to the public manifest.

#### Disclaimer
This is a stable, semantically versioned API. 

WRLD may make changes to the API from time to time but will adhere to [Semantic Versioning](http://semver.org/) and provide backward compatible end points for as long as possible. 

---

#### Contact us
If you have any problems or queries please [raise an issue](https://github.com/wrld3d/wrld-routing-api/issues/new).
