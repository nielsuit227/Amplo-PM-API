![amplo-logo](https://amplo.ch/wp-content/uploads/2020/07/28july-normal-1.png)


# Amplo AI Management Portal
## Overview
Welcome to the official documentation of the Amplo AI  Management Portal.
This documentation is solely focused on the API integration of the API which are managed on the portal. 
The APIs rely on deployed Machine Learning models (as per the AI Management Portal).
Our APIs accept `application/json` and `multipart/form-data` request data, return `json-encoded` responses and use standard `http` response codes.

## Endpoints
All our endpoints use the `base url: https://api.amplo.ch`.

### Automated Diagnostics
Endpoint: `POST /diagnose/`

This endpoint analyses one or multiple log files for all `deployed` Automated Diagnostics models. 
Logs are attached as `multipart/form-data` and should be listed under `files`. 
The response is JSON-encoded and contains for all provided files, the probability of failure calculated by all models.
This endpoint uses the latest version of all models. 

#### Request Attributes
Parameter | Description
---|---
client | Client identifier, as specified in the Amplo Portal
device | Device identifier, as specified in the Amplo Portal


#### Example requests:

CLI: 
```
curl -H "content-type: multipart/form-data" -F client=Amplo -F device=dev01 -F files=@example_log.csv https://api.amplo.ch/diagnose/
---
{"example_log":{"model_1":91,"model_2":22,"model_3":3}}
```
JS: 
```javascript
let formData = new FormData();
formData.append('client', 'amplo');
formData.append('device', 'dev01');
formData.append('files', example_log);
axios.post(
    'https://api.amplo.ch/diagnose/', 
    formData, 
    {headers: {"Content-Type": 'multipart/form-data'}}
).then(response => {
    console.log(response.data);
}); 
--- 
{ example_log: { model_1: 91, model_2: 22, model_3: 3 } }
```
## Authentication
Most of our APIs use `API keys` to authenticate requests. 
You can view and manage your API keys in the Amplo Portal. 
The API Key should be added in the header of the request, under `X-Api-Key`.
If you have an API that uses JWT, head over to https://docs.amplo.ch/jwt/ for documentation.

#### Example:
CLI:
```cli
curl -H "X-Api-Key: as0q8982kWaS23SQWwe0J2EDas21" https://api.amplo.ch/validateKey/
---
API Key validated!
```
JS:
```js
axios.post('https://api.amplo.ch/validateKey', {}, {headers: {'X-Api-Key': 'as0q8982kWaS23SQWwe0J2EDas21'}})
---
API Key validated!
```

## Error messages
Status Code | Summary
---|---
200 - OK | Everything went well. 
400 - Bad Request | The request was incorrect, often due to incorrect or missing required parameters.
401 - Unauthorized | Authorization was incorrect.
402 - Request Failed | The request was correct but failed.
403 - Forbidden | Authorization was correct, but does not have the right permissions.
500 - Server Error | Something went wrong on our side. 





