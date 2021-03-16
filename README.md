![amplo-logo](https://amplo.ch/wp-content/uploads/2020/07/28july-normal-1.png)


# Amplo Quality Management APIs
## Overview
Welcome to the official documentation of the Amplo AI-based Quality Management API.
Our APIs rely on Machine Learning models which need to be `'deployed'` on the Amplo Portal (https://portal.amplo.ch).
Our APIs accept `application/json` and `multipart/form-data` request data, return `json-encoded` responses and use standard `http` response codes.

## Endpoints
All our endpoints use the `base url: https://api.amplo.ch`.

### Automated Diagnostics
Endpoint: `POST /getDiagnosis/`

This endpoint analyses one or multiple log files for all `deployed` Automated Diagnostics models. 
Logs are attached as `multipart/form-data` and should be listed under `files`. 
The response is JSON-encoded and contains for all provided files, the probability of failure calculated by all models.
This endpoint uses the latest version of all models. 

#### Request Attributes
Parameter | Description
---|---
group | Client identifier, as specified in the Amplo Portal
device | Device identifier, as specified in the Amplo Portal


#### Example requests:

CLI: 
```
curl -H "content-type: multipart/form-data" -F group=Amplo -F device=dev01 -F files=@example_log.csv https://api.amplo.ch/getDiagnosis/
---
{"example_log":{"model_1":91,"model_2":22,"model_3":3}}
```
JS: 
```javascript
let formData = new FormData();
formData.append('group', 'amplo');
formData.append('device', 'dev01');
formData.append('files', example_log);
axios.post(
    'https://api.amplo.ch/getDiagnosis', 
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





