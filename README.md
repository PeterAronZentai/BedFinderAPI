#BedFinder API

##Tier1

### REST API

#### Submitter API
##### Create profile
Request
```
POST /profiles
```
Payload
```
{
    "age":42,
    "gender":"male",
    "sex":"female"
}
Response
```json
{
    "profileId":123
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
GET /requests
```
Response
```json
{
    result: [
        {
            requestId: 1,
            profiles: [1,3,4]
        }
    ],
    totalCount: 3
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


##### Cancel a request
Request
```
DELETE /requests/%requestId%
```


### Client API

#### Submitter
```javascript

bedfinder.createProfile({})
         .then(/* you get the profile ID here, place code to handle OK result*/)
         .fail(/* place code to handler ERROR result */)
         
bedfinder.requestBed( { profileId: [1234,2345], maximumWaitTime: "15m" })
         .then(function() { /* you get the request ID here */ })
         .fail(function() { /* you get request errors here */ })
         
var monitor = bedfinder.monitorReqest({ requestId: [1,2,3] })

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

```

##Tier2