---
site: https://anypoint.mulesoft.com/apiplatform/popular/admin/#/dashboard/apis/7782/versions/7918/portal/pages/6527/edit
apiNotebookVersion: 1.1.66
title: Repos part 4
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

API.createClient('client', '/apiplatform/repository/public/organizations/30/apis/7782/versions/7918/definition');

```



Name and Id of repository which is operated by the notebook.



```javascript

repoName = "API Notebook Test Repository 4"

repoId = "API-Notebook-Test-Repository-4"

```

```javascript

clientId = prompt("Please, enter your Client ID")

clientSecret = prompt("Please, enter your Client Secret")

```

```javascript

API.authenticate(client,"oauth_2_0",{

  clientId: clientId,

  clientSecret: clientSecret,
  
  

})

```



Retrieve name of the authenticated user



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



Create a Blob



```javascript

postBlobsResponse = client.repos.owner( currentUserId ).repo( repoId ).git.blobs.post({

  "content": "var time = new Date()//Notebook test blob",

  "encoding": "utf-8"

})

```

```javascript

assert.equal( postBlobsResponse.status, 201 )

blobShaCode = postBlobsResponse.body.sha

```



Get a Blob.

Since blobs can be any arbitrary binary data, the input and responses for

the blob API takes an encoding parameter that can be either utf-8 or

base64. If your data cannot be losslessly sent as a UTF-8 string, you can

base64 encode it.



```javascript

blobResponse = client.repos.owner( currentUserId ).repo( repoId ).git.blobs.shaCode( blobShaCode ).get()

```

```javascript

assert.equal( blobResponse.status, 200 )

```



Now we are going to commit the newly created blob into the new branch. Retrieve a _tree_ array of the parent tree. This array can be understood as a list of files which are contained in the branch at the moment of commit.



Step 1. List all commits and pick one that would become a parent.



```javascript

//Commit step 1

commitsResponse = client.repos.owner( currentUserId ).repo( repoId ).commits.get()

```

```javascript

assert.equal( commitsResponse.status, 200 )

parentCommit = commitsResponse.body[0]

commitSha = parentCommit.sha

```



Step 2. Retrieve a tree which corresponds to the parent commit.



```javascript

//Commit step 2

parentTreeSha = parentCommit.commit.tree.sha

```



Step 3. Retrieve a _tree_ array of the parent tree. This array can be understood as a list of files which are contained in the branch at the moment of commit.



```javascript

//Commit step 3

treeResponse = client.repos.owner( currentUserId ).repo( repoId ).git.trees.shaCode(parentTreeSha).get()

treeArray = treeResponse.body.tree

```



Step 4. Add our file to the list.



```javascript

//Commit step 4

treeArray.push({

      "path": "test_file.js",

      "mode": "100755",

      "type": "blob",

      "sha": blobShaCode

    })

```



Step 5. Create a tree with the extended file list which is a descendant of the parent tree.



```javascript

//Commit step 5

postTreesResponse = client.repos.owner( currentUserId ).repo( repoId ).git.trees.post({

  "base_tree": parentTreeSha,

  "tree": treeArray

})

```

```javascript

assert.equal( postTreesResponse.status, 201 )

newTreeSha = postTreesResponse.body.sha

```

```javascript

treeResponse = client.repos.owner( currentUserId ).repo( repoId ).git.trees.shaCode( newTreeSha ).get()

```

```javascript

assert.equal( treeResponse.status, 200 )

```



Step 6. Create a commit which is a descendant of the parent commit and refers to the newly created tree.



```javascript

//Commit step 6

postCommitsResponse = client.repos.owner( currentUserId ).repo( repoId ).git.commits.post({

  "message": "API Notebook test commit",

  "parents": [

    parentCommit.sha

  ],

  "tree": newTreeSha

})

```

```javascript

assert.equal( postCommitsResponse.status, 201 )

newCommitSha = postCommitsResponse.body.sha

```







One step remains -- to create a reference that points to the newly created commit. This step will be performed a bit later.



Now let's switch to some other methods.



Get a commit.



```javascript

shaCodeResponse = client.repos.owner( currentUserId ).repo( repoId ).git.commits.shaCode( newCommitSha ).get()

```

```javascript

assert.equal( shaCodeResponse.status, 200 )

```



Get all References



```javascript

refsResponse = client.repos.owner( currentUserId ).repo( repoId ).git.refs.get()

```

```javascript

assert.equal( refsResponse.status, 200 )

```



Create a Reference.



```javascript

ref = "heads/new_test_branch"

postRefsResponse = client.repos.owner( currentUserId ).repo( repoId ).git.refs.post({

  "ref": "refs/" + ref,

  "sha": parentCommit.sha

})

```

```javascript

assert.equal( postRefsResponse.status, 201 )

refSha = postRefsResponse.body.object.sha

```



Get a Reference



```javascript

refResponse = client.repos.owner( currentUserId ).repo( repoId ).git.refs.ref( ref ).get()

```

```javascript

assert.equal( refResponse.status, 200 )

```



Commit, step 7. Let's update our reference so that it pointed to our newly created commit.



Update a Reference



```javascript

patchRefResponse = client.repos.owner( currentUserId ).repo( repoId ).git.refs.ref( ref ).patch({

  "sha": newCommitSha,

  "force": true

})

```

```javascript

assert.equal( patchRefResponse.status, 200 )

```



Delete a Reference 

Example: Deleting a branch: DELETE /repos/octocat/Hello-World/git/refs/heads/feature-a 

Example: Deleting a tag:        DELETE /repos/octocat/Hello-World/git/refs/tags/v1.0



```javascript

deleteRefResponse = client.repos.owner( currentUserId ).repo( repoId ).git.refs.ref( ref ).delete()

```

```javascript

assert.equal( deleteRefResponse.status, 204 )

```



Get list of repository events



```javascript

eventsResponse = client.repos.owner( currentUserId ).repo( repoId ).events.get()

```

```javascript

assert.equal( eventsResponse.status, 200 )

```



Garbage collection. Delete a repository.



```javascript

client.repos.owner( currentUserId ).repo( repoId ).delete()

```