
# Post \#61235853 [Link](https://stackoverflow.com/questions/61235853/)

## How to invoke Cloud Function from Cloud Scheduler with Authentication

**Vote**: 16 (306/702) **Views**: 9887 (417/702) 

**Internal ID** \#1-3-246

Created at 2020-04-15 18:19:31

Tags: `python` `authentication` `google-cloud-functions` `google-cloud-scheduler`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

I've looked everywhere and it seems people either use pubsub, app engine http or http with no auth. Not too many people out there showing their work for accessing functions via authentication w/ oidc tokens to access google functions. 

I checked out: [Cannot invoke Google Cloud Function from GCP Scheduler](https://stackoverflow.com/questions/60055422/cannot-invoke-google-cloud-function-from-gcp-scheduler) but nothing seemed to work.

Documentation I followed:
[https://cloud.google.com/scheduler/docs/http-target-auth#using-gcloud_1](https://cloud.google.com/scheduler/docs/http-target-auth#using-gcloud_1)


1. created a new service account
2. set roles (Cloud scheduler service agent/Cloud functions service agent/Cloud scheduler admin/cloud functions invoker...even tried owner!)
3. deployed google function that doesn't allow public (unauthenticated) access (a simple helloworld function)
4. setup cron job on cloud scheduler to run every minute against the new deployed function with this configuration: url = helloworld function oidc-token newly created service account audience set to hello world function url



outcome on cloud scheduler logs:

```
Expand all | Collapse all{
 httpRequest: {
 }
 insertId: "ibboa4fg7l1s9"  
 jsonPayload: {
  @type: "type.googleapis.com/google.cloud.scheduler.logging.AttemptFinished"   
  jobName: "projects/project/locations/region/jobs/tester"   
  status: "PERMISSION_DENIED"   
  targetType: "HTTP"   
  url: "https://region-project.cloudfunctions.net/tester"   
 }
 logName: "projects/project/logs/cloudscheduler.googleapis.com%2Fexecutions"  
 receiveTimestamp: "2020-04-15T17:50:14.287689800Z"  
 resource: {…}  
 severity: "ERROR"  
 timestamp: "2020-04-15T17:50:14.287689800Z"
```


I saw one solution that showed someone creating a new project to get to this to work, are there any others??

Appreciate any help provided.



[New Google Function - running in central (same as my app engine app)](https://i.stack.imgur.com/8ZRRI.png)

[New Service Account - w/ Owner role](https://i.stack.imgur.com/30vXE.png)

[New Scheduled Task - Info](https://i.stack.imgur.com/1Qtr3.png)

[New Scheduled Task - Status](https://i.stack.imgur.com/60cRd.png)

[New Scheduled Task - Logs](https://i.stack.imgur.com/2tXjp.png)



If you're missing the cloudscheduler service account (ex:
service-1231231231412@gcp-sa-cloudscheduler.iam.gserviceaccount.com)
Http auth tasks wont work.
To fix, I had to disable api and renable and it gave me the service account,
I didnt use this service account but, that was the only changing factor after I did this to make it work.


----------
        
## Answer \#0

**Accepted** Vote: 8

Created at 2020-10-27 15:53:35

------------

These are the exact steps you have to take. Be sure not to skip the second step, it sets invoker permissions on the service account so that the scheduler is able to invoke the HTTP Cloud Function with that service account's OIDC information. Note: for simplicity, I choose the default service account here, however, it would be wise to create a separate service account for this purpose with less privileges.
```
# Create cloud function
gcloud functions deploy my_function \
  --entry-point=my_entrypoint \
  --runtime=python37 \
  --trigger-http \
  --region=europe-west1 \
  --project=${PROJECT_ID}

# Set invoke permissions
gcloud functions add-iam-policy-binding my_function \
  --region=europe-west1 \
  --member=serviceAccount:${PROJECT_ID}@appspot.gserviceaccount.com \
  --role="roles/cloudfunctions.invoker" \
  --project=${PROJECT_ID}

# Deploy scheduler
gcloud scheduler jobs create http my_job \
  --schedule="every 60 minutes" \
  --uri="https://europe-west1-${PROJECT_ID}.cloudfunctions.net/my_function/" \
  --http-method=POST \
  --oidc-service-account-email="${PROJECT_ID}@appspot.gserviceaccount.com" \
  --oidc-token-audience="https://europe-west1-${PROJECT_ID}.cloudfunctions.net/my_function" \
  --project=${PROJECT_ID}
```



------------
    
    
## Answer \#1

 Vote: 8

Created at 2020-12-17 07:04:51

------------

I thought I'd expand on Nebulastic's answer as I just discovered there are a few edge cases where their answer isn't complete.

### Create Cloud Function


```
# Create cloud function
gcloud functions deploy my_function \
  --entry-point=my_entrypoint \
  --runtime=python37 \
  --trigger-http \
  --region=europe-west1 \
  --project=${PROJECT_ID}
```

You may want to change fields such as `entry-point`, `runtime` or `trigger`. Read more in the [Cloud Functions deploy docs](https://cloud.google.com/sdk/gcloud/reference/functions/deploy).

### Create Cloud Scheduler job:


```
# Deploy scheduler
gcloud scheduler jobs create http my_job \
  --schedule="every 60 minutes" \
  --uri="https://europe-west1-${PROJECT_ID}.cloudfunctions.net/my_function/" \
  --http-method=POST \
  --oidc-service-account-email="${SERVICE_ACCOUNT_EMAIL}" \
  --oidc-token-audience="https://europe-west1-${PROJECT_ID}.cloudfunctions.net/my_function" \
  --project=${PROJECT_ID}
```

See [Cloud Schedule docs](https://cloud.google.com/sdk/gcloud/reference/scheduler/jobs/create/http) for more.
Note that any service account can be used for the OIDC authentication, not only the default one created for the project.
Furthermore, you don't need to call `gcloud functions add-iam-policy-binding` like suggested in Nebulastic's answer, but instead you can set the [Cloud Functions Invoker](https://cloud.google.com/functions/docs/reference/iam/roles) role to the service account passed to `--oidc-service-account-email` (see [doc on adding roles](https://cloud.google.com/iam/docs/granting-changing-revoking-access)). This way such service account would have permissions to call any Cloud Functions without needing to grant such permission on each deployment. This isn't to say the method Nebulastic suggests is incorrect, you can also do it that way.
You can verify which service accounts have permission to call the function by clicking on its name in the Cloud Functions list -> Permissions tab -> Roles subtab -> open Cloud Functions Invoker row.
[](https://i.stack.imgur.com/WIX3Hl.png)

---


Now for the edge cases:

### Ingress - Allow internal traffic only


Despite what's suggested by [the documentation](https://cloud.google.com/functions/docs/networking/network-settings#ingress_settings), setting function's ingress settings to 'Allow internal traffic only' does not encompass Cloud Scheduler's traffic and will result in PERMISSION_DENIED error. This is something GCP devs are aware of and it may be fixed in the future. For now, use 'Allow all traffic' (or `--ingress-settings=all` if deploying using gcloud).
[](https://i.stack.imgur.com/yvh20l.png)

### URL parameters and OIDC authentication


If your Cloud Schedule job uses OIDC authentication and your function expects arguments in form of [URL parameters](https://en.wikipedia.org/wiki/Query_string) - eg. `...my_function?key=value` - then you must ensure the OIDC Audience  contain the URL parameters. Specify the URL parameters in the URL field only, but if they appear in Audience field, the request will return 403 UNAUTHENTICATED. Note that OIDC Audience is auto-filled in with a copy of the URL if you don't specify the OIDC Audience manually. This is another issue GCP devs know about and are potentially working on a fix or an update to the documentation.
tldr: don't put URL parameters in OIDC audience URL - only in the URL field:
```
--uri="https://europe-west1-${PROJECT_ID}.cloudfunctions.net/my_function?key=value" \
  --oidc-token-audience="https://europe-west1-${PROJECT_ID}.cloudfunctions.net/my_function" \
```


### JSON parameters and Content-Type


If you choose to use a Cloud Scheduler job calling a function with JSON parameters - eg. `{"key": "value"}` in the  field - then you will need to either:
- Parse the JSON manually in the function - for instance [Flask's get_json](https://flask.palletsprojects.com/en/1.1.x/api/#flask.Request.get_json) won't work unless you call it with `force=True`.- Create the function using `gcloud scheduler jobs create`, rather than from the Console, and ensure you specify the Content-Type header by adding the following flag: `--headers Content-Type=application/json`

### Cloud Scheduler API enabled before 19/03/2019


> The Cloud Scheduler service account with this role granted is automatically set up when you enable the Cloud Scheduler API, unless you enabled it prior to March 19, 2019, in which case you must add the role manually.
This seems to be the reason behind the issue listed in the author's question. See [docs](https://cloud.google.com/scheduler/docs/http-target-auth) for more. The reason this caused issues is as follows:
> Cloud Scheduler itself must have a service account of its own that has the Cloud Scheduler Service Agent role granted. This is so it can generate header tokens on behalf of your client service account to authenticate to your target.

---





------------
    
    
## Answer \#2

 Vote: 3

Created at 2021-03-19 14:05:08

------------

This is a frustrating bug that has been resolved for those who activated the api after 3/19/2019.
For those before 3/19/2019 google has noted the fix below;
This process allows you to resolve the issue without disabling and re-enabling the API.
Google has a document that addresses this issue and states
"This is necessary only if you enabled Cloud Scheduler API prior to March 19, 2019."
[https://cloud.google.com/scheduler/docs/http-target-auth#add](https://cloud.google.com/scheduler/docs/http-target-auth#add)

[](https://i.stack.imgur.com/dO6EQ.png)

> Find your project number:
```
gcloud projects describe [project-id] --format='table(projectNumber)'
Replacing [project-id] with your project ID.
```

> Copy down the number.
> Grant the Cloud Scheduler service account the Cloud Scheduler Service Agent role, using the project number you copied down:
```
gcloud projects add-iam-policy-binding [project-id] --member serviceAccount:service-[project-number]@gcp-sa-cloudscheduler.iam.gserviceaccount.com --role roles/cloudscheduler.serviceAgent
```

> Replacing [project-id] with your project ID and [project-number] with the project number from above.
Note from comments above:
Disabling and re-enabling the API also resolves the issue (as it does this procedure automatically) but it is disruptive for anybody who doesn't want to destroy their current tasks.


------------
    
    
## Answer \#3

 Vote: 1

Created at 2022-04-20 15:13:08

------------

Just wanted to share that I was running into similar similar authentication issues but due to a different cause. I had followed all the docs exactly as outlined, setting up the service account, giving it access to invoke the specific cloud run function, setting the auth header accordingly when creating the cloud scheduler job. Was seeing this error message:
```
{
    httpRequest: {
    status: 401
    }
    insertId: "1xxol5ifezru6m"
    jsonPayload: {
    @type: "type.googleapis.com/google.cloud.scheduler.logging.AttemptFinished"
    jobName: "projects/<PROJECT_ID>/locations/us-central1/jobs/test"
    status: "UNAUTHENTICATED"
    targetType: "HTTP"
    url: "<MY_URL>"
    }
    logName: "projects/<PROJECT_ID>/logs/cloudscheduler.googleapis.com%2Fexecutions"
    receiveTimestamp: "2022-04-19T02:09:54.932289922Z"
    resource: {
    labels: {
    job_id: "test"
    location: "us-central1"
    project_id: "<PROJECT_ID>"
    }
    type: "cloud_scheduler_job"
    }
    severity: "ERROR"
    timestamp: "2022-04-19T02:09:54.932289922Z"
}
```

Here is how I was creating the cloud scheduler job:
```
gcloud scheduler jobs create http senses-test-run \
  --oidc-service-account-email="senses-cloud-scheduler@<PROJECT_ID>.iam.gserviceaccount.com" \
  --oidc-token-audience="https://my-custom-domain-that-maps-to-cloud-run" \
  --location="us-central1" \
  --schedule="5 4 * * mon" \
  --uri="https://my-custom-domain-that-maps-to-cloud-run" \
  --http-method="post" \
  --headers="Content-Type=application/json" \
  --message-body="{\"foo\":\"bar\",\"beep\":\"boop\"}"
```

I had a custom domain mapped to my cloud run function, so instead of the scheduler job posting to the generated URL from GCP, i.e. `https://<cloud-run-name>-XXXXXX-uc.a.run.app`, it would post to `https://my-custom-domain-that-maps-to-cloud-run`. This works fine when specifying the `uri` that your cloud scheduler will post to, but not that `oidc-token-audience`.
Here's what [the docs](https://cloud.google.com/sdk/gcloud/reference/scheduler/jobs/create/http) say about `oidc-token-audience`:
> --oidc-token-audience=OIDC_TOKEN_AUDIENCE
The audience to be used when generating an OpenId Connect token to be included in the request sent to the target when executing the job. If not specified, the URI specified in target will be used.
For reasons unbeknownst to me, this did not work when I put `https://my-custom-domain-that-maps-to-cloud-run`. I had to use the provided cloud run URL for my service, `https://<cloud-run-name>-XXXXXX-uc.a.run.app`, instead. As soon as I changed the audience URL, the authentication issues went away.
Hopefully this saves someone the hours it cost me to figure this out!


------------
    
    
## Answer \#4

 Vote: -1

Created at 2020-04-15 20:41:48

------------

1.I created a clod function (in the same region as my App Engine Application), which does not allow public access

2.I created a service account with Owner role

3.I created the cloud scheduler job:

url = [https://europe-west2-my-project.cloudfunctions.net/my-function](https://europe-west2-my-project.cloudfunctions.net/my-function) 

oidc-token

newly created service account

audience = [https://europe-west2-my-project.cloudfunctions.net/my-function](https://europe-west2-my-project.cloudfunctions.net/my-function) 

[](https://i.stack.imgur.com/5KDOGm.png)

Everything worked as expected. Please double check your set up because it should work. 


------------
    
    