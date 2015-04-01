---
site: https://anypoint.mulesoft.com/apiplatform/popular/admin/#/dashboard/apis/7782/versions/7918/portal/pages/6521/edit
apiNotebookVersion: 1.1.66
title: Search, events, feeds, meta, notifications
---

```javascript
load('https://github.com/chaijs/chai/releases/download/1.9.0/chai.js')
```

See http://chaijs.com/guide/styles/ for assertion styles

```javascript
assert = chai.assert
```

```javascript
// Read about the GitHub at http://api-portal.anypoint.mulesoft.com/onpositive/api/github
API.createClient('client', '#REF_TAG_DEFENITION');
```

```javascript
clientId = prompt("Please, enter your Client ID")
clientSecret = prompt("Please, enter your Client Secret")
```

```javascript
API.authenticate(client,"oauth_2_0",{
  clientId: clientId,
  clientSecret: clientSecret
})
```

Retreive name of the authenticated user

```javascript
currentUserId = client.user.get().body.login
```

Search code

```javascript
codeResponse = client.search.code.get({
  "q": "Date language:js user:"+currentUserId
})
```

```javascript
assert.equal( codeResponse.status, 200  )
```

Search repositories

```javascript
repositoriesResponse = client.search.repositories.get({
  "q": "RAMLTest"
})
```

```javascript
assert.equal( repositoriesResponse.status, 200  )
```

Search users

```javascript
usersResponse = client.search.users.get({
  "q": "Tom"
})
```

```javascript
assert.equal( usersResponse.status, 200  )
```

Find issues by state and keyword. (This method returns up to 100 results per page.

```javascript
issuesResponse = client.search.issues.get({
  "q": "bug"
})
```

```javascript
assert.equal( issuesResponse.status, 200  )
```

List public events

```javascript
eventsResponse = client.events.get()
```

```javascript
assert.equal( eventsResponse.status, 200  )
```

List Feeds.
GitHub provides several timeline resources in Atom format. The Feeds API
 lists all the feeds available to the authenticating user.

```javascript
feedsResponse = client.feeds.get()
```

```javascript
assert.equal( feedsResponse.status, 200  )
```

This gives some information about GitHub.com, the service

```javascript
metaResponse = client.meta.get()
```

```javascript
assert.equal( metaResponse.status, 200  )
```

Get your current rate limit status
Note: Accessing this endpoint does not count against your rate limit.

```javascript
rate_limitResponse = client.rate_limit.get()
```

```javascript
assert.equal( rate_limitResponse.status, 200  )
```

List issues.
List all issues across all the authenticated user's visible repositories.

```javascript
issuesResponse = client.issues.get({
  "filter": "all",
})
```

client.

List your notifications.
List all notifications for the current user, grouped by repository.

```javascript
notificationsResponse = client.notifications.get({ "all": true })
```

```javascript
assert.equal( notificationsResponse.status, 200  )
```

```javascript
threadId = "null"
```

Mark as read.
Marking a notification as "read" removes it from the default view on GitHub.com.

**Attentions** We do not test this method as it causes irreversable changes of the authenticated user's account.

```javascript
putNotificationsResponse = client.notifications.put({"last_read_at": "2012-09-25T07:54:41-07:00"})
```

```javascript
assert.equal( putNotificationsResponse.status, 205  )
```

**Attention**
We need some _notification_ object for executing methods of the  _/notifications/threads/id_ resource, so you should get ensured that the _notificationsResponse.body_ is not empty. That's how notifications are induced:

Users receive notifications for discussions in repositories they watch including:
1. Issues and their comments
2. Pull Requests and their comments
3. Comments on any commits

Remember that users do not receive notifications for their own issues, comments and pull requests. 
Thus, in order to induce a notification, some GitHub user, other then the authenticated one, should perform one of the three actions mentioned above.

Let's retrieve the notifications list one more time.

```javascript
notificationsResponse = client.notifications.get({ "all": true })
```

Select notification if there exist any.

```javascript
threadId = null
if(notificationsResponse.body.length>0){
  threadId = notificationsResponse.body[0].id
}
```

View a single thread

```javascript
if(threadId){
  threadResponse = client.notifications.threads.id( threadId ).get()
}
```

```javascript
if(threadId){
  assert.equal( threadResponse.status, 200  )
}
```

Mark a thread as read. Do not execute the method if you do not want to do so.

```javascript
if(threadId){
  patchThreadResponse = client.notifications.threads.id( threadId ).patch({})
}
```

```javascript
if(threadId){
  assert.equal( patchThreadResponse.status, 205  )
}
```

Set a Thread Subscription.
This lets you subscribe to a thread, or ignore it. Subscribing to a thread
is unnecessary if the user is already subscribed to the repository. Ignoring
a thread will mute all future notifications (until you comment or get @mentioned).

```javascript
if(threadId){
  putSubscriptionResponse = client.notifications.threads.id( threadId ).subscription.put({
    "subscribed": true,
    "ignored": false
  })
}
```

```javascript
if(threadId){
  assert.equal( putSubscriptionResponse.status, 200  )
}
```

Get a Thread Subscription

```javascript
if(threadId){
  subscriptionResponse = client.notifications.threads.id( threadId ).subscription.get()
}
```

```javascript
if(threadId){
  assert.equal( subscriptionResponse.status, 200  )
}
```

Delete a Thread Subscription. Do not execute the method if you do not want to do so.

```javascript
if(threadId){
  deleteSubscriptionResponse = client.notifications.threads.id( threadId ).subscription.delete()
}
```

```javascript
if(threadId){
  assert.equal( deleteSubscriptionResponse.status, 204  )
}
```