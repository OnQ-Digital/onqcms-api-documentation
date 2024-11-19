# API Job

Some API requests to the ONQ CMS will need to be processed asynchronnously outside the Post Request.
This requests create a job on the CMS that can be regularly polled until the job is complete. The 
job response object contains the status of the job, a payload containing the data returned by the 
original request once the job is completed, and an array of errors encountered while processing 
the job (if any). 

- [Fetch Job](#fetch-job)

[Home](README.md)

## Job Response JSON Object

| Parameter    | Type            | Required  | Description |
|--------------|-----------------|-----------|-------------|
| status       | string          | True | The status of the job which will either be "active" while the job is in progress or "done" once the job has completed. Any errors are delegated to the endpoint that generated the request and will be in the **errors** field - a request to an endpoint that failed will still have a job status of "Complete".
| payload      | Object          | True  | The 
| errors       | Array (String)  | True | An array containing any errors generated by the original endpoint. Will be empty if no errors. |

### Example Job Response Object
```
{
    "status":"done",
    "payload":{...},
    "errors":[...]
}
```
## Fetch Job

### Endpoint: job/fetch
### Response: [Job Object](#job-response-json-object)

### Request Parameters

| Parameter   | Type    | Required | Description |
|-------------|---------|----------|-------------|
| job_id      | string  | True     | The job id provided by a request to a job-returning endpoint. |

### Example Request
```
curl -H "Authorization: bearer demo1234" -X POST -d '{
    "job_id":"job123",
}' \
https://onqcms.com/api/content/update
```
The above snippet retrieves the information for the job with the id **job123**.

[Top](#content)