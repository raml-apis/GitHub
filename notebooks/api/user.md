---
site: https://anypoint.mulesoft.com/apiplatform/popular/admin/#/dashboard/apis/7782/versions/7918/portal/pages/6519/edit
apiNotebookVersion: 1.1.66
title: User
---

```javascript
load('https://github.com/chaijs/chai/releases/download/1.9.0/chai.js')
```

See http://chaijs.com/guide/styles/ for assertion styles

```javascript
assert = chai.assert
```

GitHub id of a special github account which is used in our tests.

```javascript
notebookUserId = "api-notebook-user"
```

Name and Id of repository which is operated by the notebook.

```javascript
repoName = "API Notebook Test Repository 7"
repoId = "API-Notebook-Test-Repository-7"
```

Title and value of a public key which is operated by the notebook.

```javascript
keyTitle = "API Notebook test public key"
keyValue = "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAAAgQCCoHjh/dHsAYbgQCRS7gKABBuxoQjmhQX/qUdwVESpqHUuJCgexcM7t9DN21jQkQywez1yAtYHCBWzrlkFA2w9jsnqdMXIosoSCG6eHAlid/edvpuonvZd1Jj7dPhCpY1WxLw3ixsjh9Z5VIOAxuWoDwO4NkjDZ4zd7vtImmfK4Q== RSA-1024"
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
  clientSecret: clientSecret,
  scopes: [ "user", "repo", "delete_repo", "admin:public_key" ]
})
```

Get the authenticated user

```javascript
userResponse = client.user.get()
```

```javascript
assert.equal( userResponse.status, 200 )
ownerId = userResponse.body.login
```

Update the authenticated user

```javascript
/*
Here we pass empty object as argument, as we do not want to affect your personal data.
If you wish to call the method sufficiently, you may pass for example the following argument:
{
  company: "My New Company",
  blog: "My new blog"
}
for complete list of the user object fields refer to the variable userResponse.body initialized two code blocks above
*/
patchUserResponse = client.user.patch({})
```

```javascript
assert.equal( patchUserResponse.status, 200 )
```

Follow a user.

```javascript
putFollowingResponse = client.user.following.username( notebookUserId ).put()
```

```javascript
assert.equal( putFollowingResponse.status, 204 )
```

Check if you are following a user

```javascript
followingUserResponse = client.user.following.username( notebookUserId ).get()
```

```javascript
assert.equal( followingUserResponse.status, 204 )
```

List who the authenticated user is following

```javascript
followingResponse = client.user.following.get()
```

```javascript
assert.equal( followingResponse.status, 200 )
```

Unfollow a user.

```javascript
deleteFollowingResponse = client.user.following.username( notebookUserId ).delete()
```

```javascript
assert.equal( deleteFollowingResponse.status, 204 )
```

Let's delete a repository which could have been created during previous notebook runs.

```javascript
client.repos.owner( ownerId ).repo( repoId ).delete()
```

Create a new repository for the authenticated user.

```javascript
repoCreateResponse = client.user.repos.post({
  "name": repoName
})
```

```javascript
assert.equal( repoCreateResponse.status, 201 )

```

List repositories for the authenticated user. Note that this does not include
repositories owned by organizations which the user can access. You can lis
user organizations and list organization repositories separately.

```javascript
reposResponse = client.user.repos.get()
```

```javascript
assert.equal( reposResponse.status, 200 )
```

List public and private organizations for the authenticated user

```javascript
orgsResponse = client.user.orgs.get()
```

```javascript
assert.equal( orgsResponse.status, 200 )
```

List all public repositories.
This provides a dump of every public repository, in the order that they
were created.
Note: Pagination is powered exclusively by the since parameter. is the
Link header to get the URL for the next page of repositories.

```javascript
publicRepositoriesResponse = client.repositories.get()
```

```javascript
assert.equal( publicRepositoriesResponse.status, 200 )
```

Star a repository

```javascript
putStarRepoResponse = client.user.starred.owner( ownerId ).repo( repoId ).put()
```

```javascript
assert.equal( putStarRepoResponse.status, 204 )
```

List repositories being starred by the authenticated user

```javascript
starredResponse = client.user.starred.get()
```

```javascript
assert.equal( starredResponse.status, 200 )
```

Check if you are starring a repository

```javascript
starredRepoResponse = client.user.starred.owner( ownerId ).repo( repoId ).get()
```

```javascript
assert.equal( starredRepoResponse.status, 204 )
```

Unstar a repositor

```javascript
unstarResponse = client.user.starred.owner( ownerId ).repo( repoId ).delete()
```

```javascript
assert.equal( unstarResponse.status, 204 )
```

List your public keys.
Lists the current user's keys.

```javascript
keysResponse = client.user.keys.get()
```

```javascript
assert.equal( keysResponse.status, 200 )
```

Let's delete a public key which could have been created during previous notebook runs.

```javascript
if( Object.prototype.toString.call( keysResponse.body ) === '[object Array]' ){
  for(var ind in keysResponse.body){
    var key = keysResponse.body[ind]
    var l = key.key.length
    if(keyValue.slice(0,l)==key.key){
      client.user.keys.keyId( key.id ).delete()
    }
  }
}
```

Create a public key.

```javascript
keyCreateResponse = client.user.keys.post({
  "title": keyTitle,
  "key": keyValue
})
```

```javascript
assert.equal( keyCreateResponse.status, 201 )
keyId = keyCreateResponse.body.id
```

Get a single public key

```javascript
keyResponse = client.user.keys.keyId( keyId ).get()
```

```javascript
assert.equal( keyResponse.status, 200 )
```

Delete a public key

```javascript
deleteKeyResponse = client.user.keys.keyId( keyId ).delete()
```

```javascript
assert.equal( deleteKeyResponse.status, 204 )
```

List email addresses for a user.
In the final version of the API, this method will return an array of hashes
with extended information for each email address indicating if the address
has been verified and if it's primary email address for GitHub.
Until API v3 is finalized, use the application/vnd.github.v3 media type to
get other response format.

```javascript
emailsResponse = client.user.emails.get()
```

```javascript
assert.equal( emailsResponse.status, 200 )
```

Let's generate a dummy email so that we could test email adding ad removing.

```javascript
dummyEmail = ""
{
  var email = emailsResponse.body[0].email
  var ind = email.indexOf('@')
  dummyEmail = email.substring(0,ind)+"@somehost.com"
}
```

Add email address(es).
You can post a single email address or an array of addresses.

```javascript
postEmailsResponse = client.user.emails.post( [ dummyEmail ] )
```

```javascript
assert.equal( postEmailsResponse.status, 201 )
```

Delete email address(es).
You can include a single email address or an array of addresses.

```javascript
emailsResponse = client.user.emails.delete( [ dummyEmail ] )
```

```javascript
assert.equal( emailsResponse.status, 204 )
```

Let's create an issue before retreiving a list of issues.

```javascript
client.repos.owner( ownerId ).repo( repoId ).issues.post({
  "title": "API Notebook Test Issue",
  "body": "This issue was created by the API Notebook during /users testing",
  "assignee": ownerId,
  "labels": [
    "Label1",
    "Label2"
  ]
})
```

List all issues across owned and member repositories for the authenticated user.

```javascript
issuesResponse = client.user.issues.get({
  "filter": "all",
})
```

```javascript
assert.equal( issuesResponse.status, 200 )
```

Watch a repository

```javascript
watchResponse = client.user.subscriptions.owner(ownerId ).repo( repoId ).put()
```

```javascript
assert.equal( watchResponse.status, 204 )
```

List repositories being watched by the authenticated user

```javascript
subscriptionsResponse = client.user.subscriptions.get()
```

```javascript
assert.equal( subscriptionsResponse.status, 200 )
```

Check if you are watching a repository

```javascript
watchingResponse = client.user.subscriptions.owner( ownerId ).repo( repoId ).get()
```

```javascript
assert.equal( watchingResponse.status, 204 )
```

Stop watching a repositor

```javascript
stopWatchingResponse = client.user.subscriptions.owner(ownerId ).repo( repoId ).delete()
```

```javascript
assert.equal( stopWatchingResponse.status, 204 )
```

Delete the repository

```javascript
deleteRepoResponse = client.repos.owner( ownerId ).repo( repoId ).delete()
```

```javascript
assert.equal( deleteRepoResponse.status, 204 )
```

List all of the teams across all of the organizations to which the authenticated user belongs. This method requires user or repo scope when authenticating via OAuth

```javascript
teamsResponse = client.user.teams.get()
```

```javascript
assert.equal( teamsResponse.status, 200 )
```

List the authenticated user's follower

```javascript
followersResponse = client.user.followers.get()
```

```javascript
assert.equal( followersResponse.status, 200 )
```