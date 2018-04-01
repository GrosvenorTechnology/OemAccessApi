




Entity State Requests
---------------------

This endpoint is used to query the value of a state of an entity maintained on
the server

**Supported Types and states:**

System.Mode [IsActive]

**Request:**

GET
/grosvenor-oem/configuration/\<namespace\>/\<typeName\>/\<id\>/states/\<statename\>

**Request headers:**

-   Authorization

**Response Body:**

{

"isActive": true

}

**Response Codes:**

200: The current value of the state is returned.

401: Authorised header not supplied

403: Supplied authorisation credentials does not allow access to this resource

404: Requested resource does not exist, i.e. unknown system mode id





