#BedFinder API

### UPDATE: Based on discussion with PZ 6/19

#### Create a need
    
     POST /needs
          - caseReferenceId
          - assessments
               - assessment
               - assessment

#### View all my needs
     
     GET /needs
          params: status=open | closed (default open?)
                    expand = true|false

#### View a specific need

     GET /needs/:needId
     
#### Edit a need

     PUT /needs/:needId

#### Create a request

     POST /need/:needId/requests

#### View requests for a need

     GET /need/:needId/requests
     (includes responses?)

#### View all my sent requests

     GET /requests/sent
          ?status = open | closed | canceled

#### View a request

     GET /requests/:requestId

#### Edit the request details

     PUT /requests/:requestId

#### View the request responses

     GET /requests/:requestId/responses
          ?response=yes|possible|no (default is all)

#### Cancel a request (???)

     POST /requests/:requestId/cancel (???)
          - cancellation reason


#### Get agencies that my org is connected to

     GET /agencies

#### Get specific agency info

     GET /agencies/:agencyId

#### Get groups that my org belongs to

     GET /groups

#### Get specific agency group

     GET /groups/:groupId

#### Get all agencies in a group

     GET /groups/:groupId/agencies


#### View the requests that were sent to my agency from other agencies

     GET /requests/received
          ?status = open| closed

#### Respond to a request

     POST /requests/:requestId/responses

#### Change the response

     PUT /responses/:responseId

#### QUESTIONS: 
     - Expanding resources
          - decision: use query param "expand" with true/false
     - Versioning
          - put version number in the URL
     - Paging
          - don't need right away for v1
     - Agencies vs Locations? Locations respond, not agencies.
          - don't need to handle this right away, but might inform the data model / nomenclature


## Tier1


### Submitter API

#### REST API

##### Create assesments
Request
```
POST /assesments
```
Payload
```
{
    "age":42,
    "gender":"male",
    "sex":"female"
}
```
Response
```json
{
    "profileId":123
}
```

##### Get a profile
Request
```
GET /profiles/%profileId%
```
Response
```json
{
    "age":42,
    "gender":"male"
}
```

##### Create a request
Request
```
POST /requests
```
Payload
```json
{
    "profileId": [123,345]
}
```
Response
```json
{
    "requestId": "R1",
    "totalResponders": 42
}
```

##### Get all my active requests
Request
```
GET /requests/mine
```
Response
```json
{
    "result": [
        {
            "requestId": 1,
            "profiles": [1,3,4]
        }
    ],
    "totalCount": 3
}
```
##### Get specific response
Request
```
GET /responses/1
```
Response
```json
{
    "result": {
            "responseId": 12,
            "result": "reject",
            "reason": "no spaces available"
        }
}
```

##### Get responses for my request
Request
```
GET /responses/for-request/1
```
Response
```json
{
    "result": [
        {
            "responseId": 12,
            "result": "reject",
            "reason": "no spaces available"
        }
    ],
    "requestId": 1,
    "totalCount": 3
}
```

##### Start monitoring a request
Request
```
GET /request/monitor/1
UPGRADE WebSocker 2.0
```
```json
{
    "requestMonitorUrl":"/requests/1",
    "lastModified": "UTC-Date"
}
```
##### Cancel a request
Request
```
DELETE /requests/%requestId%
```


#### Client API

```javascript


bedfinder.createProfile(/* profile data in json*/)
         .then(function() { /* you get the profile ID here, place code to handle OK result*/})
         .fail(function() { /* place code to handler ERROR result */})
         
bedfinder.requestBed( { profileId: [1234,2345], maximumWaitTime: "15m" })
         .then(function() { /* you get the request ID here */ })
         .fail(function() { /* you get request errors here */ })
         
var monitor = bedfinder.monitorReqest(1)
//
//var monitor = bedfinder.monitorReqest([1,2,3], {monitorOptions})

monitor.on("success", function(requestId, responderId) {
    //place code to handle success (eg remove outstanding record from screen, play audio alert)    
})

monitor.on("fail", function(requestId) {
    //place code to handle when a request has entirely failed (all responders rejected or timed out)
});

monitor.on("reject", function(requestId, rejectionInfo) {
    //place code to handle a potential responser's rejection
});

monitor.on("timeout", function(requestId) {
    //place code to potentially add some more wait time
});

bedfinder.getRequestFromMe({includeProfiles: true})
         .then(function(requests) {
             requests.forEach(function(request) {
                 //display request details with a templating engine
             });
         });
```
### Responder API
#### REST API
Get active requests
```
 GET /requests/others?$include=profileId
```
Response
```json
{
   result: [{
    "requestId":1,
    "profileId":[1,2,3]
   }, {
    "requestId":2,
    "profileId":[4,5,6]
   }]
}

Claim  a request

Uri
```
POST /requests/%requestId%/claim
```
Payload
```json
    {
        "comment": "any message related to the response"
    }
```
Response
```json
    {
    }
```

Reject a request

Uri
```
POST /requests/%requestId%/reject
```
Payload
```json
    {
        "comment": "any message related to the response"
    }
```
Response
```json
    {
    }
```

#### Client API 
```javascript
var monitor bedfinder.createMonitor()

//var monitor bedfinder.createMonitor({ groupId: [1,2,3,4] });
monitor.on("newRequest", function(requestDetails) {
    //place code to display profiles
    var profileId : requestDetails.profileId
});

bedfinder.getRequestsFromOthers()
         .then(function(requests) {
             request.forEach(request) {
                 $('#requestTable').append(
                    handlebars.render(request, "<div>Request Date {{RequestDate}}</div>"); 
                  })
             });
         })
         .fail(function() {
         });

bedfinder.getProfile(profileId)
         .then(function(profileInfo) {
           //handle when a profile is received
         })
         .fail(function(error) {
             //handle when an error occured
         });

bedfinder.claim({
        requestId: 1,
        comment: "...."
     })
    .then(function() {
        //handle when your response
    });
    
bedfinder.reject({
        requestId: 1,
        reason: "no more beds"
     })
    .then(function() {
        //handle when your response
    });

```

##Tier2
