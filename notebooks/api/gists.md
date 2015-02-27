---
site: https://anypoint.mulesoft.com/apiplatform/popular/admin/#/dashboard/apis/7782/versions/7918/portal/pages/6523/preview
apiNotebookVersion: 1.1.66
title: Gists
---

```javascript
load('https://github.com/chaijs/chai/releases/download/1.9.0/chai.js')
```

See http://chaijs.com/guide/styles/ for assertion styles

```javascript
assert = chai.assert
```

```javascript
// Read about the GitHub at https://anypoint.mulesoft.com/apiplatform/popular/admin/#/dashboard/apis/7782/versions/7918/contracts
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

Create a gist

```javascript
postGistsResponse = client.gists.post({
  "description": "the description for this gist",
  "files": {
    "file1.txt": {
      "content": "file 1 content"
    },
    "file2.txt": {      
      "content": "file 2 content"
    },
    "file3.txt": {
      "content": "file 3 content"
    }
  }
})
```

```javascript
assert.equal( postGistsResponse.status, 201 )
gistId = postGistsResponse.body.id
```

List the authenticated user's gists or if called anonymously, this will
return all public gists.

```javascript
gistsResponse = client.gists.get()
```

```javascript
assert.equal( gistsResponse.status, 200 )
```

Get a single gist

```javascript
gistResponse = client.gists.id( gistId ).get()
```

```javascript
assert.equal( gistResponse.status, 200 )
```

Edit a gist

```javascript
patchGistsResponse = client.gists.id( gistId ).patch({
  "description": "the description for this gist",
  "files": {
    "file1.txt": {
      "content": "file 1 content updated"
    },
    "file2.txt": {
      "filename": "file2_updated.txt",
      "content": "file 2 content"
    },
    "file3.txt": {
      
    },
    "file4.txt": {
      "content": "file 4 content"
    }
  }
})
```

```javascript
//assert.equal( patchGistsResponse.status, 200 )
```

Star a gist

```javascript
starResponse = client.gists.id( gistId ).star.put()
```

```javascript
assert.equal( starResponse.status, 204 )
```

Check if a gist is starred

```javascript
starResponse = client.gists.id( gistId ).star.get()
```

```javascript
assert.equal( starResponse.status, 204 )
```

List the authenticated user's starred gists

```javascript
starredResponse = client.gists.starred.get()
```

```javascript
assert.equal( starredResponse.status, 200 )
```

Unstar a gist

```javascript
starResponse = client.gists.id( gistId ).star.delete()
```

```javascript
assert.equal( starResponse.status, 204 )
```

Create a comment


```javascript
postCommentsResponse = client.gists.id( gistId ).comments.post({
  "body": "Test gist comment"
})
```

```javascript
assert.equal( postCommentsResponse.status, 201 )
commentId = postCommentsResponse.body.id
```

List comments on a gist

```javascript
commentsResponse = client.gists.id( gistId ).comments.get()
```

```javascript
assert.equal( commentsResponse.status, 200 )
```

Get a single comment

```javascript
gistCommentResponse = client.gists.id( gistId ).comments.commentId( commentId ).get()
```

```javascript
assert.equal( gistCommentResponse.status, 200 )
```

Edit a comment

```javascript
patchCommentResponse = client.gists.id( gistId ).comments.commentId( commentId ).patch({
  "body": "New editing comment test"
})
```

```javascript
//assert.equal( patchCommentResponse.status, 200 )
```

Delete a comment

```javascript
deleteCommentResponse = client.gists.id( gistId ).comments.commentId( commentId ).delete()
```

```javascript
assert.equal( deleteCommentResponse.status, 204 )
```

List all public gists

```javascript
publicGistsResponse = client.gists.public.get()
```

```javascript
assert.equal( publicGistsResponse.status, 200 )
publicGistId = publicGistsResponse.body[0].id
```

Fork a gist

```javascript
forksResponse = client.gists.id( publicGistId ).forks.post()
```

```javascript
assert.equal( forksResponse.status, 201 )
```

Delete a gist

```javascript
deleteGistResponse = client.gists.id( gistId ).delete()
```

```javascript
assert.equal( deleteGistResponse.status, 204 )
```

Garbage collection. Delete the created fork.

```javascript
forkGistId = forksResponse.body.id
deleteGistResponse = client.gists.id( forkGistId ).delete()
```