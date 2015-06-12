#BedFinder API

##Tier1

### REST API

#### Create profile

```
POST /profiles
```

#### Create a request
```
POST /requests
```

```json
{
    "profileId": [123,345]
}
```
### Client API

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

monitor.on("reject", function(requstId, rejectionInfo) {
    
});

monitor.on("timeout", function(requstId) {
    
});

```




##Tier2