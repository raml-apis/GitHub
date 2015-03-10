---
site: https://anypoint.mulesoft.com/apiplatform/popular/admin/#/dashboard/apis/7782/versions/7918/portal/pages/6524/edit
apiNotebookVersion: 1.1.66
title: Repos part 1
---

```javascript
load('https://github.com/chaijs/chai/releases/download/1.9.0/chai.js')
```

See http://chaijs.com/guide/styles/ for assertion styles

```javascript
assert = chai.assert
```

Name and Id of repository which is operated by the notebook.

```javascript
repoName = "API Notebook Test Repository 1"
repoId = "API-Notebook-Test-Repository-1"
```

Title and value of a repository public key which is operated by the notebook.

```javascript
keyTitle = "API Notebook key for repo"
keyValue = "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAAAgQCyo97VumdNiq0x9yi3uNOLYIK5Gwqp24ID/huHpCzd8ZLniGUF42YRGiyq5iu0jQX2I6ZImGOR0bccg++grLKZNuJIayDOaWTmA1HCx+fOAWsfT9yZ0sAb7HPLBtiiEEtQXOUrH4AW9fX5tlYzXzezzBCOXFnYHzozbDcpIvNWww== RSA-1024"
```

GitHub id of a special github account which is used in our tests.

```javascript
notebookUserId = "api-notebook-user"
```

Name and Id of repository which is operated by the notebook.

```javascript
// Read about the GitHub at http://api-portal.anypoint.mulesoft.com/onpositive/api/github
API.createClient('client', '/apiplatform/repository/public/organizations/30/apis/7782/versions/7918/definition');
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

List all public repositories.
This provides a dump of every public repository, in the order that they were created.

```javascript
repositoriesResponse = client.repositories.get()
```

```javascript
assert.equal( repositoriesResponse.status, 200 )
```

Retreive name of the authenticated user

```javascript
currentUserId = client.user.get().body.login
```

Let's delete a repository which could have been created during earlier notebook runs.

```javascript
client.repos.owner( currentUserId ).repo( repoId ).delete()
```

Create a repository

```javascript
postReposResponse = client.user.repos.post({
  "name": repoName,
  "auto_init": true
})
```

```javascript
assert.equal( postReposResponse.status, 201 )
```

Get a repository.

```javascript
repoResponse = client.repos.owner( currentUserId ).repo( repoId ).get()
```

```javascript
assert.equal( repoResponse.status, 200 )
```

Edit repository

```javascript
patchReposResponse = client.repos.owner( currentUserId ).repo( repoId ).patch({
  "description" : "Updated repository description",
  "name": repoId
})
```

```javascript
assert.equal( patchReposResponse.status, 200 )
```

List.
When authenticating as an organization owner of an organization-owned
repository, all organization owners are included in the list of
collaborators. Otherwise, only users with access to the repository are
returned in the collaborators list.

```javascript
collaboratorsResponse = client.repos.owner( currentUserId ).repo( repoId ).collaborators.get()
```

```javascript
assert.equal( collaboratorsResponse.status, 200 )
```

Add collaborator

```javascript
putCollaboratorResponse = client.repos.owner( currentUserId ).repo( repoId ).collaborators.user(notebookUserId).put()
```

```javascript
assert.equal( putCollaboratorResponse.status, 204 )
```

Check if user is a collaborator

```javascript
collaboratorResponse = client.repos.owner( currentUserId ).repo( repoId ).collaborators.user(notebookUserId).get()
```

```javascript
assert.equal( collaboratorResponse.status, 204 )
```

Remove collaborator

```javascript
deleteCollaboratorResponse = client.repos.owner( currentUserId ).repo( repoId ).collaborators.user(notebookUserId).delete()
```

```javascript
assert.equal( deleteCollaboratorResponse.status, 204 )
```

In order to create a new branch we need a parent commit.

```javascript
commitsResponse = client.repos.owner( currentUserId ).repo( repoId ).commits.get()
parentCommit = commitsResponse.body[0]
commitSha = parentCommit.sha
```

Now we can create a new branch.

```javascript
newBranchResponse = client.repos.owner( currentUserId ).repo( repoId ).git.refs.post({
  ref: "refs/heads/new_test_branch",
  sha: commitSha
})
```

In order to create a pull request from _new_test_branch_ to  _master_ we need to commit some changes into _new_test_branch_. For example, let's create a "new_test_file.js" file in  _new_test_branch_.

```javascript
testFileName = "new_test_file.js"
contentsCreatePathResponse = client.repos.owner( currentUserId ).repo( repoId ).contents.path( testFileName ).put({
  "message" : "This commit is performed by the API Notebook. Create file." ,
  "content" : "dmFyIGRhdGUgLSBuZXcgRGF0ZSgpLy9BUEkgbm90ZWJvb2sgdGVzdCBmaWxl",
  "branch" : "new_test_branch"
})
```

Now we can create a pull request.

```javascript
postPullsResponse = client.repos.owner( currentUserId ).repo( repoId ).pulls.post({
  "title" : "Notebook Test Pull Request" ,
  "body" : "This pull request was induced by the API Notebook." ,
  "head" : currentUserId+":new_test_branch" ,
  "base" : "master"
})
```

```javascript
assert.equal( postPullsResponse.status, 201 )
number = postPullsResponse.body.number
```

List pull requests

```javascript
pullsResponse = client.repos.owner( currentUserId ).repo( repoId ).pulls.get()
```

```javascript
assert.equal( pullsResponse.status, 200 )
```

Get a single pull request

```javascript
pullResponse = client.repos.owner( currentUserId ).repo( repoId ).pulls.number( number ).get()
```

```javascript
assert.equal( pullResponse.status, 200 )
```

Update a pull request

```javascript
patchNumberResponse = client.repos.owner( currentUserId ).repo( repoId ).pulls.number( number ).patch({
  "title": pullResponse + " updated"
})
```

```javascript
// assert.equal( patchNumberResponse.status, 200 )
```

List pull requests files

```javascript
filesResponse = client.repos.owner( currentUserId ).repo( repoId ).pulls.number( number ).files.get()
```

```javascript
assert.equal( filesResponse.status, 200 )
```

List commits on a pull request

```javascript
pullCommitsResponse = client.repos.owner( currentUserId ).repo( repoId ).pulls.number( number ).commits.get()
```

```javascript
assert.equal( pullCommitsResponse.status, 200 )
```

Create a comment.

```javascript
postCommentsResponse = client.repos.owner( currentUserId ).repo( repoId ).pulls.number( number ).comments.post({
  "body": "This comment was created by the API Notebook",
  "commit_id": pullCommitsResponse.body[0].sha,
  "path": testFileName,
  "position": 1
})
```

```javascript
assert.equal( postCommentsResponse.status, 201 )
```

List comments on a pull request

```javascript
commentsResponse = client.repos.owner( currentUserId ).repo( repoId ).pulls.number( number ).comments.get()
```

```javascript
assert.equal( commentsResponse.status, 200 )
```

Merge a pull request (Merge Button's)

```javascript
putMergeResponse = client.repos.owner( currentUserId ).repo( repoId ).pulls.number( number ).merge.put({
  "commit_message" : "Test API Notebook pull request merge"
})
```

```javascript
assert.equal( putMergeResponse.status, 200 )
```

Get if a pull request has been merged

```javascript
mergeResponse = client.repos.owner( currentUserId ).repo( repoId ).pulls.number( number ).merge.get()
```

```javascript
assert.equal( mergeResponse.status, 204 )
```

List comments in a repository.
By default, Review Comments are ordered by ascending ID.

```javascript
commentsResponse = client.repos.owner( currentUserId ).repo( repoId ).pulls.comments.get()
```

```javascript
assert.equal( commentsResponse.status, 200 )
commentId = commentsResponse.body[0].id
```

Get a single comment

```javascript
commentResponse = client.repos.owner( currentUserId ).repo( repoId ).pulls.comments.commentId( commentId ).get()
```

```javascript
assert.equal( commentResponse.status, 200 )
```

Edit a comment

```javascript
patchCommentResponse = client.repos.owner( currentUserId ).repo( repoId ).pulls.comments.commentId( commentId ).patch({
  "body": commentResponse.body.body + " Updated"
})
```

```javascript
assert.equal( patchCommentResponse.status, 200 )
```

Delete a comment

```javascript
deleteCommentResponse = client.repos.owner( currentUserId ).repo( repoId ).pulls.comments.commentId( commentId ).delete()
```

```javascript
assert.equal( deleteCommentResponse.status, 204 )
```

Create a hook

```javascript
postHooksResponse = client.repos.owner( currentUserId ).repo( repoId ).hooks.post({
  "name": "web",
  "active": true,
  "events": [
    "push",
    "pull_request"
  ],
  "config": {
    "url": "http://example.com/webhook",
    "content_type": "json"
  }
})
```

```javascript
assert.equal( postHooksResponse.status, 201 )
```

Get list of hooks

```javascript
hooksResponse = client.repos.owner( currentUserId ).repo( repoId ).hooks.get()
```

```javascript
assert.equal( hooksResponse.status, 200 )
```

Get single hook

```javascript
hookId = hooksResponse.body[0].id
```

```javascript
hookResponse = client.repos.owner( currentUserId ).repo( repoId ).hooks.hookId( hookId ).get()
```

```javascript
assert.equal( hookResponse.status, 200 )
```

Edit a hook

```javascript
patchHookResponse = client.repos.owner( currentUserId ).repo( repoId ).hooks.hookId( hookId ).patch({
  "active": true,
  "add_events": [
    "pull_request"
  ]
})
```

```javascript
assert.equal( patchHookResponse.status, 200 )
```

Test a push hook.
This will trigger the hook with the latest push to the current repository
if the hook is subscribed to push events. If the hook is not subscribed
to push events, the server will respond with 204 but no test POST will
be generated.
Note: Previously /repos/:owner/:repo/hooks/:id/test

```javascript
testsResponse = client.repos.owner( currentUserId ).repo( repoId ).hooks.hookId( hookId ).tests.post()
```

```javascript
assert.equal( testsResponse.status, 204 )
```

Delete a hook

```javascript
deleteHookResponse = client.repos.owner( currentUserId ).repo( repoId ).hooks.hookId( hookId ).delete()
```

```javascript
assert.equal( deleteHookResponse.status, 204 )
```

Let's delete a public key which could have been created during previous notebook runs.

```javascript
{
  var response = client.repos.owner( currentUserId ).repo( repoId ).keys.get()
  for(var ind in response.body){
    var key = response.body[ind]
    var l = key.key.length
    if(keyValue.slice(0,l)==key.key){
      client.repos.ownerId( currentUserId ).repoId( repoId ).keys.keyId( key.id ).delete()
    }
  }
}
```

Create a key

```javascript
postKeysResponse = client.repos.owner( currentUserId ).repo( repoId ).keys.post({
  "title": keyTitle,
  "key": keyValue
})
```

```javascript
assert.equal( postKeysResponse.status, 201 )
keyId = postKeysResponse.body.id
```

Get list of keys

```javascript
keysResponse = client.repos.owner( currentUserId ).repo( repoId ).keys.get()
```

```javascript
assert.equal( keysResponse.status, 200 )
```

Get a key

```javascript
keyResponse = client.repos.owner( currentUserId ).repo( repoId ).keys.keyId( keyId ).get()
```

```javascript
assert.equal( keyResponse.status, 200 )
```

Delete a key

```javascript
deleteKeyResponse = client.repos.owner( currentUserId ).repo( repoId ).keys.keyId( keyId ).delete()
```

```javascript
assert.equal( deleteKeyResponse.status, 204 )
```

Garbage collection. Delete a repository.

```javascript
repositoryDeleteResponse = client.repos.owner( currentUserId ).repo( repoId ).delete()
```

```javascript
assert.equal( repositoryDeleteResponse.status, 204 )
```