---
site: https://anypoint.mulesoft.com/apiplatform/popular/admin/#/dashboard/apis/7782/versions/7918/portal/pages/6529/preview
apiNotebookVersion: 1.1.66
title: Repos (part 6)
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

repoName = "API Notebook Test Repository 6"

repoId = "API-Notebook-Test-Repository-6"

```

```javascript

clientId = prompt("Please, enter your Client ID")

clientSecret = prompt("Please, enter your Client Secret")

```

```javascript

// Read about the GitHub at https://anypoint.mulesoft.com/apiplatform/popular/admin/#/dashboard/apis/7782/versions/7918/contracts

API.createClient('client', '/apiplatform/repository/public/organizations/30/apis/7782/versions/7918/definition');

```

```javascript

API.authenticate(client,"oauth_2_0",{

 clientId: clientId,

  clientSecret: clientSecret

})

```

```javascript

API.createClient('GitHubUploadAPIv3', '/apiplatform/repository/public/organizations/30/apis/7785/versions/7921/definition');

```

```javascript

API.authenticate(GitHubUploadAPIv3,"oauth_2_0",{

  clientId: clientId,

  clientSecret: clientSecret

})

```



Retreive name of the authenticated user



```javascript

currentUserId = client.user.get().body.login

```



Let's delete a repository which could have been created during earlier notebook runs.



```javascript

client.repos.ownerId( currentUserId ).repoId( repoId ).delete()

```



Create a repository



```javascript

postReposResponse = client.user.repos.post({

  "name": repoName,

  "auto_init": true

})

```



Create a fork.

Forking a Repository happens asynchronously. Therefore, you may have to wait

a short period before accessing the git objects. If this takes longer than 5

minutes, be sure to contact Support.



```javascript

postForksResponse = client.repos.ownerId( currentUserId ).repoId( repoId ).forks.post()

```

```javascript

assert.equal( postForksResponse.status, 202 )

```



List forks



```javascript

forksResponse = client.repos.ownerId( currentUserId ).repoId( repoId ).forks.get()

```

```javascript

assert.equal( forksResponse.status, 200 )

```



Pick the initial commit.



```javascript

commitsResponse = client.repos.ownerId( currentUserId ).repoId( repoId ).commits.get()

assert.equal( commitsResponse.status, 200 )

initialCommitSha = commitsResponse.body[0].sha

```



Create new branch.



```javascript

newBranch = "new_test_branch"

newRef = "refs/heads/" + newBranch

```

```javascript

newBranchResponse = client.repos.ownerId( currentUserId ).repoId( repoId ).git.refs.post({

  ref: newRef,

  sha: initialCommitSha

})

```



Commit a file into the new branch.



```javascript

path = "test_file.js"

contentsCreatePathResponse = client.repos.ownerId( currentUserId ).repoId( repoId ).contents.path( path ).put({

  "message" : "This commit is performed by the API Notebook. Create file." ,

  "content" : "dmFyIGRhdGUgLSBuZXcgRGF0ZSgpLy9BUEkgbm90ZWJvb2sgdGVzdCBmaWxl",

  "branch" : newBranch

})

newCommitSha = contentsCreatePathResponse.body.commit.sha

```



Compare two commits



```javascript

commitsCompareResponse = client("/repos/{ownerId}/{repoId}/compare/{baseId}...{headId}",{

  "ownerId": currentUserId,

  "repoId": repoId,

  "baseId": "master",

  "headId": newBranch

}).get()

```

```javascript

assert.equal( commitsCompareResponse.status, 200 )

```



Users with pull access can view deployments for a repository



```javascript

deploymentsResponse = client.repos.ownerId( currentUserId ).repoId( repoId ).deployments.get({}, {

  headers: {

    "Accept": "application/vnd.github.cannonball-preview+json"

  }

})

```

```javascript

assert.equal( deploymentsResponse.status, 200 )

```



Users with push access can create a deployment for a given ref



```javascript

postDeploymentsResponse = client.repos.ownerId( currentUserId ).repoId( repoId ).deployments.post({

  "ref" : "refs/heads/new_test_branch" ,

  "payload" : {

    "environment" : "production" ,

    "deploy_user" : "atmos" ,

    "room_id" : 123456

  } ,

  "description" : "API Notebook test deployment."

}, {

  headers: {

    "Accept": "application/vnd.github.cannonball-preview+json"

  }

})

```

```javascript

assert.equal( postDeploymentsResponse.status, 201 )

deploymentId = postDeploymentsResponse.body.id

```



Create a Deployment Status

Users with push access can create deployment statuses for a given deployment:



```javascript

postDeploymentStatusesResponse = client.repos.ownerId( currentUserId ).repoId( repoId ).deployments.id( deploymentId ).statuses.post({

  "state": "success",

  "target_url": "https://example.com/deployment/42/output",

  "description": "Deployment finished successfully."

}, {

  headers: {

    "Accept": "application/vnd.github.cannonball-preview+json"

  }

})

```

```javascript

assert.equal( postDeploymentStatusesResponse.status, 201 )

```



Users with pull access can view deployment statuses for a deployment



```javascript

deploymentStatusesResponse = client.repos.ownerId( currentUserId ).repoId( repoId ).deployments.id( deploymentId ).statuses.get({}, {

  headers: {

    "Accept": "application/vnd.github.cannonball-preview+json"

  }

})

```

```javascript

assert.equal( deploymentStatusesResponse.status, 200 )

```



Users with push access to the repository will receive all releases (i.e., published releases and draft releases). Users with pull access will receive published releases only



```javascript

releasesResponse = client.repos.ownerId( currentUserId ).repoId( repoId ).releases.get()

```

```javascript

assert.equal( releasesResponse.status, 200 )

```



Create a release

Users with push access to the repository can create a release.



```javascript

postReleaseResponse = client.repos.ownerId( currentUserId ).repoId( repoId ).releases.post({

  "tag_name": "v1.0.0",

  "target_commitish": "master",

  "name": "v1.0.0",

  "body": "Description of the release",

  "draft": false,

  "prerelease": false

})

```

```javascript

assert.equal( postReleaseResponse.status, 201 )

releaseId = postReleaseResponse.body.id

```



Get a single release



```javascript

idResponse = client.repos.ownerId( currentUserId ).repoId( repoId ).releases.id( releaseId ).get()

```

```javascript

assert.equal( idResponse.status, 200 )

```



Users with push access to the repository can edit a release



```javascript

patchReleaseResponse = client.repos.ownerId( currentUserId ).repoId( repoId ).releases.id( releaseId ).patch({

  "tag_name": "v1.0.0",

  "target_commitish": "master",

  "name": "v1.0.0",

  "body": "Description of the patch release",

  "draft": false,

  "prerelease": false

})

```

```javascript

assert.equal( patchReleaseResponse.status, 200 )

```



Upload a release asset

This is a unique endpoint. The request domain changes from "api.github.com" to "uploads.github.com". You need to use an HTTP client which supports SNI to make calls to this endpoint.

The asset data is expected in its raw binary form, rather than JSON. Everything else about the endpoint is the same. Pass your authentication exactly the same as the rest of the API.



```javascript

postAssetsResponse = GitHubUploadAPIv3.repos.ownerId( currentUserId ).repoId( repoId ).releases.id( releaseId ).assets.post({

  "field1": "value1"

} , {

  query: {

    "name": "testRAML5.json"

  }})

```

```javascript

assert.equal( postAssetsResponse.status, 201 )

```



List assets for a release



```javascript

assetsResponse = client.repos.ownerId( currentUserId ).repoId( repoId ).releases.id( releaseId ).assets.get()

```

```javascript

assert.equal( assetsResponse.status, 200 )

assetId = assetsResponse.body[0].id

```



Get a single release asset



```javascript

assetResponse = client.repos.ownerId( currentUserId ).repoId( repoId ).releases.assets.id( assetId ).get()

```

```javascript

assert.equal( assetResponse.status, 200 )

```



Edit a release asset

Users with push access to the repository can edit a release asset.



```javascript

patchAssetResponse = client.repos.ownerId( currentUserId ).repoId( repoId ).releases.assets.id( assetId ).patch({

  "name" : "foo-1.0.0-osx.zip" ,

  "label" : "Mac binary"

})

```

```javascript

assert.equal( patchAssetResponse.status, 200 )

```



Delete a release asset



```javascript

deleteAssetResponse = client.repos.ownerId( currentUserId ).repoId( repoId ).releases.assets.id( assetId ).delete()

```

```javascript

assert.equal( deleteAssetResponse.status, 204 )

```



Users with push access to the repository can delete a release



```javascript

deleteReleaseResponse = client.repos.ownerId( currentUserId ).repoId( repoId ).releases.id( releaseId ).delete()

```

```javascript

assert.equal( deleteReleaseResponse.status, 204 )

```



Garbage collection. Delete a repository.



```javascript

client.repos.ownerId( currentUserId ).repoId( repoId ).delete()

```