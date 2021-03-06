**Scenario**: an external component wants to know when deployments are complete. It registers a callback url that will be called each time a deployment has finished, with the deployment information on whether the deployment was successful or not, the author, commit id and more.

**Possible use case**: With this feature you're able to hook a kudu site as a trigger in web services such as [zapier.com](https://zapier.com) which allows you to do a wide array of actions when a deployment is done, for example:
- Send an email when deployment is done
- Call when a deployment has failed
- Tweet when a deployment has succeeded
- And more...

## API exposed by the Kudu service ##

### Subscribe / Add Hook ###

    POST /api/hooks

**Body**

```
{
  "url": "http://www.callback.com/callback",
  "event": "PostDeployment",
  "insecure_ssl": false (set to true to ignore https certificate check, for test purposes only)
}
```

**Response**

201 Created or 409 Conflict (if url already exists as the address of a hook but the event is different)

```
{
  "id": 123,
  "url": "http://www.callback.com/callback",
  "event": "PostDeployment"
}
```

***

### Unsubscribe / Remove Hook ###

    DELETE /api/hooks/[id]

**Response**

200 OK

***

### List Hooks ###

    GET /api/hooks

**Response**

200 OK

```
[
  {
    "id": 123,
    "url": "http://www.callback.com/callback",
    "event": "postDeployment"
  },
  {
    ...
  }
  ...
]
```

***

### Get Hook ###

    GET /api/hooks/[id]

**Response**

200 OK

```
{
  "id": 123,
  "url": "http://www.callback.com/callback",
  "event": "PostDeployment",
  "last_datetime": "08/08/2013 13:24PM",
  "last_status": "OK",
  "last_reason": "internal server SSL issue or whatever",
  "last_context": "The last event content published"
}
```

***

### Publish Hook Type ###

    POST /api/hooks/hooks/publish/[hook type]

**Body**

```
{
  ...
}
```

**Response**

200 OK


***

## Hook request fired by the Kudu service ##

This is request sent by the Kudu service to the registered URL.

    POST [url]

**Body**

```
{
  "id": "cd5bee7181e74ea38a3522e73253f6ebb8ed72fb",
  "status": "success", (could be pending, building, deploying, failed, success)
  "author_email": "someone@somewhere.com",
  "author": "Some One",
  "message": "My fix",
  "deployer": "Some One",
  "start_time": "2013-06-06T01:24:16.5873293Z",
  "end_time": "2013-06-06T01:24:17.63342Z"
}
```

## Hook scenarios

### Notify deployment status to Slack

See https://github.com/WCOMAB/KuduPostDeploymentSlackHook for details