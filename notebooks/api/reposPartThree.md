---
site: https://anypoint.mulesoft.com/apiplatform/popular/admin/#/dashboard/apis/7782/versions/7918/portal/pages/6526/edit
apiNotebookVersion: 1.1.66
title: Repos part 3
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

repoName = "API Notebook Test Repository 3"

repoId = "API-Notebook-Test-Repository-3"

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



List Stargazers. New implementation



```javascript

watchersResponse = client.repos.owner( currentUserId ).repo( repoId ).watchers.get()

```

```javascript

assert.equal( watchersResponse.status, 200 )

```



Let's pick a commit.



```javascript

commitsResponse = client.repos.owner( currentUserId ).repo( repoId ).commits.get()

commitSha = commitsResponse.body[0].sha

```



Now we can create a status.



```javascript

postStatusesRefResponse = client.repos.owner( currentUserId ).repo( repoId ).statuses.ref( commitSha ).post({

  "state": "success",

  "target_url": "https://example.com/build/status",

  "description": "The build succeeded!",

  "context": "continuous-integration/jenkins"

}, {

  headers: {

    "Accept": "application/vnd.github.she-hulk-preview+json"

  }})

```

```javascript

assert.equal( postStatusesRefResponse.status, 201 )

```



List Statuses for a specific Ref

Users with pull access can view commit statuses for a given ref



```javascript

statusesResponse = client.repos.owner( currentUserId ).repo( repoId ).statuses.ref( commitSha ).get({

  headers: {

    "Accept": "application/vnd.github.she-hulk-preview+json"

  }})

```

```javascript

assert.equal( statusesResponse.status, 200 )

assert( statusesResponse.body.length > 0 )

```



List downloads for a repository



```javascript

downloadsResponse = client.repos.owner( currentUserId ).repo( repoId ).downloads.get()

```

```javascript

assert.equal( downloadsResponse.status, 200 )

```



We need a new branch for our tests.



```javascript

newBranchResponse = client.repos.owner( currentUserId ).repo( repoId ).git.refs.post({

  ref: "refs/heads/new_test_branch",

  sha: commitSha

})

```



Perform a merge



```javascript

mergesResponse = client.repos.owner( currentUserId ).repo( repoId ).merges.post({

  "base" : "master" ,

  "head" : "new_test_branch" ,

  "commit_message" : "Shipped cool_feature!"

})

```

```javascript

assert.equal( mergesResponse.status, 204 )

```



List issues for a repository



```javascript

issuesResponse = client.repos.owner( currentUserId ).repo( repoId ).issues.get({

  "filter": "all",

  "state": "open",

})

```

```javascript

assert.equal( issuesResponse.status, 200 )

```



Create an issue.

Any user with pull access to a repository can create an issue.



```javascript

postIssuesResponse = client.repos.owner( currentUserId ).repo( repoId ).issues.post({

  "title" : "Notebook Test Issue" ,

  "body" : "This issue was created by the API Notebook." ,

  "labels" : [

    "Notebook" ,

    "RAML"

  ]

})

```

```javascript

assert.equal( postIssuesResponse.status, 201 )

issue = postIssuesResponse.body

number = issue.number

```



Get a single issue



```javascript

numberResponse = client.repos.owner( currentUserId ).repo( repoId ).issues.number( number ).get()

```

```javascript

assert.equal( numberResponse.status, 200 )

```



Edit an issue.

Issue owners and users with push access can edit an issue.



```javascript

patchIssueResponse = client.repos.owner( currentUserId ).repo( repoId ).issues.number( number ).patch({

  "title" : issue.title + " Updated1",

  "body" : issue.body + " Updated1.",

  "state" : "closed",

  "labels" : [

    "Notebook" ,

    "RAML"

  ]

})

```

```javascript

assert.equal( patchIssueResponse.status, 200 )

```



List issue events for a repository



```javascript

eventsResponse = client.repos.owner( currentUserId ).repo( repoId ).issues.events.get()

```

```javascript

assert.equal( eventsResponse.status, 200 )

eventId = eventsResponse.body[0].id

```



Get a single event



```javascript

eventResponse = client.repos.owner( currentUserId ).repo( repoId ).issues.events.eventId( eventId ).get()

```

```javascript

assert.equal( eventResponse.status, 200 )

```



List events for an issue.



```javascript

eventsResponse = client.repos.owner( currentUserId ).repo( repoId ).issues.number( number ).events.get()

```

```javascript

assert.equal( eventsResponse.status, 200 )

```



List labels on an issue



```javascript

labelsResponse = client.repos.owner( currentUserId ).repo( repoId ).issues.number( number ).labels.get()

```

```javascript

assert.equal( labelsResponse.status, 200 )

```



Replace all labels for an issue.

Sending an empty array ([]) will remove all Labels from the Issue.



```javascript

putLabelsResponse = client.repos.owner( currentUserId ).repo( repoId ).issues.number( number ).labels.put([

  "Notebook L2" ,

  "RAML L2"

])

```

```javascript

assert.equal( putLabelsResponse.status, 200 )

```



Add labels to an issue



```javascript

newIssueLabel = "New Test Label" 

postLabelsResponse = client.repos.owner( currentUserId ).repo( repoId ).issues.number( number ).labels.post([ newIssueLabel ])

```

```javascript

assert.equal( postLabelsResponse.status, 200 )

```



Remove a label from an issue



```javascript

deleteLabelResponse = client.repos.owner( currentUserId ).repo( repoId ).issues.number( number ).labels.name( newIssueLabel ).delete({})

```

```javascript

assert.equal( deleteLabelResponse.status, 200 )

```



Remove all labels from an issue



```javascript

deleteLabelsResponse = client.repos.owner( currentUserId ).repo( repoId ).issues.number( number ).labels.delete()

```

```javascript

assert.equal( deleteLabelsResponse.status, 204 )

```



Create a comment



```javascript

postCommentsResponse = client.repos.owner( currentUserId ).repo( repoId ).issues.number( number ).comments.post({

  "body": "This comment was created by the API Notebook."

})

```

```javascript

assert.equal( postCommentsResponse.status, 201 )

commentId = postCommentsResponse.body.id

```



List comments on an issue



```javascript

issueCommentsResponse = client.repos.owner( currentUserId ).repo( repoId ).issues.number( number ).comments.get()

```

```javascript

assert.equal( issueCommentsResponse.status, 200 )

```



List comments in a repository



```javascript

issuesCommentsResponse = client.repos.owner( currentUserId ).repo( repoId ).issues.comments.get()

```

```javascript

assert.equal( issuesCommentsResponse.status, 200 )

```



Get a single comment



```javascript

commentResponse = client.repos.owner( currentUserId ).repo( repoId ).issues.comments.commentId( commentId ).get()

```

```javascript

assert.equal( commentResponse.status, 200 )

```



Edit a comment



```javascript

patchCommenResponse = client.repos.owner( currentUserId ).repo( repoId ).issues.comments.commentId( commentId ).patch({

  "body": commentResponse.body.body + " Updated."

})

```

```javascript

assert.equal( patchCommenResponse.status, 200 )

```



Delete a comment



```javascript

deleteCommentResponse = client.repos.owner( currentUserId ).repo( repoId ).issues.comments.commentId( commentId ).delete()

```

```javascript

assert.equal( deleteCommentResponse.status, 204 )

```



Garbage collection. Delete a repository.



```javascript

client.repos.owner( currentUserId ).repo( repoId ).delete()

```