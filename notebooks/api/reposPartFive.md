---
site: https://anypoint.mulesoft.com/apiplatform/popular/admin/#/dashboard/apis/7782/versions/7918/portal/pages/6528/edit
apiNotebookVersion: 1.1.66
title: Repos part 5
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

repoName = "API Notebook Test Repository 5"

repoId = "API-Notebook-Test-Repository-5"

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

```javascript

commitsResponse = client.repos.owner( currentUserId ).repo( repoId ).commits.get()

assert.equal( commitsResponse.status, 200 )

parentCommit = commitsResponse.body[0]

commitSha = parentCommit.sha

```



Create a Tag Object.

Note that creating a tag object does not create the reference that makes a

tag in Git. If you want to create an annotated tag in Git, you have to do

this call to create the tag object, and then create the refs/tags/[tag]

reference. If you want to create a lightweight tag, you only have to create

the tag reference - this call would be unnecessary.



```javascript

postTagsResponse = client.repos.owner( currentUserId ).repo( repoId ).git.tags.post({

  "tag": "v0.0.1",

  "message": "This tag was created by API Notebook\n",

  "object": commitSha,

  "type": "commit"

})

```

```javascript

assert.equal( postTagsResponse.status, 201 )

shaCode = postTagsResponse.body.sha

```



Get a Tag



```javascript

tagResponse = client.repos.owner( currentUserId ).repo( repoId ).git.tags.shaCode( shaCode ).get()

```

```javascript

assert.equal( tagResponse.status, 200 )

```



Get list of tags



```javascript

tagsResponse = client.repos.owner( currentUserId ).repo( repoId ).tags.get()

```

```javascript

assert.equal( tagsResponse.status, 200 )

```



Set a Repository Subscription



```javascript

subscribeResponse = client.repos.owner( currentUserId ).repo( repoId ).subscription.put({

  "subscribed" : true ,

  "ignored" : false

})

```

```javascript

assert.equal( subscribeResponse.status, 200 )

```



Get a Repository Subscription



```javascript

subscriptionResponse = client.repos.owner( currentUserId ).repo( repoId ).subscription.get()

```

```javascript

assert.equal( subscriptionResponse.status, 200 )

```



Delete a Repository Subscription



```javascript

subscriptionDeleteResponse = client.repos.owner( currentUserId ).repo( repoId ).subscription.delete()

```

```javascript

assert.equal( subscriptionDeleteResponse.status, 204 )

```



Commit a file



```javascript

path = "test_file.js"

contentsCreatePathResponse = client.repos.owner( currentUserId ).repo( repoId ).contents.path( path ).put({

  "message" : "This commit is performed by the API Notebook. Create file." ,

  "content" : "dmFyIGRhdGUgLSBuZXcgRGF0ZSgpLy9BUEkgbm90ZWJvb2sgdGVzdCBmaWxl"

})

```

```javascript

assert.equal( contentsCreatePathResponse.status, 201 )

contentsSha = contentsCreatePathResponse.body.content.sha

```



Get contents.

This method returns the contents of a file or directory in a repository.

Files and symlinks support a custom media type for getting the raw content.

Directories and submodules do not support custom media types.

Note: This API supports files up to 1 megabyte in size.

Here can be many outcomes. For details see "http://developer.github.com/v3/repos/contents/"



```javascript

contentsPathResponse = client.repos.owner( currentUserId ).repo( repoId ).contents.path( path ).get()

```

```javascript

assert.equal( contentsPathResponse.status, 200 )

```



Delete a file.

This method deletes a file in a repository.



```javascript

deleteContentsResponse = client.repos.owner( currentUserId ).repo( repoId ).contents.path( path ).delete({

  "message": "This commit is performed by the API Notebook. Delete file.",

  "sha": contentsSha

})

```

```javascript

assert.equal( deleteContentsResponse.status, 200 )

```



List Stargazers



```javascript

stargazersResponse = client.repos.owner( currentUserId ).repo( repoId ).stargazers.get()

```

```javascript

assert.equal( stargazersResponse.status, 200 )

```



Get archive link.

This method will return a 302 to a URL to download a tarball or zipball

archive for a repository. Please make sure your HTTP framework is

configured to follow redirects or you will need to use the Location header

to make a second GET request.

Note: For private repositories, these links are temporary and expire quickly.



```javascript

archive_format = "zipball"

path = "master"

archive_formatPathResponse = client.repos.owner( currentUserId ).repo( repoId ).archive_format( archive_format ).path( path ).get()

```

```javascript

assert.equal( archive_formatPathResponse.status, 200 )

```



Garbage collection. Delete a repository.



```javascript

client.repos.owner( currentUserId ).repo( repoId ).delete()

```