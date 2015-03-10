---
site: https://anypoint.mulesoft.com/apiplatform/popular/admin/#/dashboard/apis/7782/versions/7918/portal/pages/6525/edit
apiNotebookVersion: 1.1.66
title: Repos part 2
---

```javascript
load('https://github.com/chaijs/chai/releases/download/1.9.0/chai.js')
```

See http://chaijs.com/guide/styles/ for assertion styles

```javascript
assert = chai.assert
```

Enter ID of organization which is operated by the notebook.
**Attention** the organization should be created manually before the notebook is launched.

```javascript
orgId = prompt("Please, enter ID of your GitHub test organization.")
```

Name and Id of repository which is operated by the notebook.

```javascript
repoName = "API Notebook Test Organization Repository"
repoId = "API-Notebook-Test-Organization-Repository"
```

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
  clientSecret: clientSecret,
  scopes: [ "admin:org", "delete_repo", "gist", "notifications", "repo", "user" ]
})
```


Let's delete a repository which could have been created during previous notebook runs.

```javascript
{
  var reposResponse = client.orgs.org( orgId ).repos.get()
  for(var ind in reposResponse.body){
    var key = reposResponse.body[ind]
    if(key.name == repoId){
      client.repos.owner( orgId ).repo( repoId ).delete()
    }
  }
}
```

Create a repository in the organization.

```javascript
postOrgReposResponse = client.orgs.org( orgId ).repos.post({
  "name": repoName,
  "description": "This is your first repository",
  "homepage": "https://github.com",
  "private": false,
  "has_issues": true,
  "has_wiki": true,
  "has_downloads": true,
  "auto_init": true,
})
```

```javascript
assert.equal( postOrgReposResponse.status, 201 )
```

```javascript
repoId = postOrgReposResponse.body.name
```

Create a milestone

```javascript
milestonesResponse = client.repos.owner( orgId ).repo( repoId ).milestones.post({
  "title" : "My test milestone" ,
  "state" : "open" ,
  "description" : "My first test milestone" ,
  "due_on" : "2014-05-09T23:39:01Z"
})
```

```javascript
assert.equal( milestonesResponse.status, 201 )
```

List milestones for a repository

```javascript
milestonesResponse = client.repos.owner( orgId ).repo( repoId ).milestones.get()
```

```javascript
assert.equal( milestonesResponse.status, 200 )
number = milestonesResponse.body[0].number
```

Get a single milestone

```javascript
milestoneResponse = client.repos.owner( orgId ).repo( repoId ).milestones.number( number ).get()
```

```javascript
assert.equal( milestoneResponse.status, 200 )
```

Update a milestone


```javascript
patchMilestoneResponse = client.repos.owner( orgId ).repo( repoId ).milestones.number( number ).patch({
  "description" : "API NOtebook Test Milestone"
})
```

```javascript
assert.equal( patchMilestoneResponse.status, 200 )
```

Create an issue.
Any user with pull access to a repository can create an issue.

We shall use the authenticated user as assagnee.

```javascript
currentUserId = client.user.get().body.login
postIssueResponse = client.repos.owner( orgId ).repo( repoId ).issues.post({
  "title": "Notebook test issue",
  "body": "This test issue was created by the API Notebook.",
  "assignee": currentUserId,
  "milestone": number,
  "labels": [
    "Label1",
    "Label2"
  ]
})
```

```javascript
assert.equal( postIssueResponse.status, 201 )
```

Get labels for every issue in a milestone.

```javascript
labelsResponse = client.repos.owner( orgId ).repo( repoId ).milestones.number( number ).labels.get()
```

```javascript
assert.equal( labelsResponse.status, 200 )
```

Delete a milestone

```javascript
deleteMilestoneResponse = client.repos.owner( orgId ).repo( repoId ).milestones.number( number ).delete()
```

```javascript
assert.equal( deleteMilestoneResponse.status, 204 )
```

We need a tree in order to create a commit.

```javascript
postTreesResponse = client.repos.owner( orgId ).repo( repoId ).git.trees.post({
  "tree": [
    {
      "path": "file.js",
      "mode": "100644",
      "type": "blob",
      "content": "dateObj = new Date()"
    }
  ]
})
```

Create a commit.

```javascript
postCommitResponse = client.repos.owner( orgId ).repo( repoId ).git.commits.post({
  "message": "my commit message",
  "tree": postTreesResponse.body.sha
})
```

```javascript
assert.equal( postCommitResponse.status, 201 )
commitShaCode = postCommitResponse.body.sha
```

List commits on a repository

```javascript
commitsResponse = client.repos.owner( orgId ).repo( repoId ).commits.get()
```

```javascript
assert.equal( commitsResponse.status, 200 )
```

Get a single commit

```javascript
shaCodeResponse = client.repos.owner( orgId ).repo( repoId ).commits.shaCode( commitShaCode ).get()
```

```javascript
assert.equal( shaCodeResponse.status, 200 )
```

Create a commit comment

```javascript
postCommentsResponse = client.repos.owner( orgId ).repo( repoId ).commits.shaCode( commitShaCode ).comments.post({
  "body": "New test comment",
  "sha": commitShaCode
})
```

```javascript
assert.equal( postCommentsResponse.status, 201 )
commentId = postCommentsResponse.body.id
```

List commit comments for a repository.
Comments are ordered by ascending ID.

```javascript
commentsResponse = client.repos.owner( orgId ).repo( repoId ).comments.get()
```

```javascript
assert.equal( commentsResponse.status, 200 )
```

Get a single commit comment

```javascript
commentResponse = client.repos.owner( orgId ).repo( repoId ).comments.commentId( commentId ).get()
```

```javascript
assert.equal( commentResponse.status, 200 )
```

Update a commit comment

```javascript
patchCommentResponse = client.repos.owner( orgId ).repo( repoId ).comments.commentId( commentId ).patch({ "body": "Comment updated." })
```

```javascript
assert.equal( patchCommentResponse.status, 200 )
```

Delete a commit comment

```javascript
deleteCommentResponse = client.repos.owner( orgId ).repo( repoId ).comments.commentId( commentId ).delete()
```

```javascript
assert.equal( deleteCommentResponse.status, 204 )
```

List languages.
List languages for the specified repository. The value on the right of a
language is the number of bytes of code written in that language.

```javascript
languagesResponse = client.repos.owner( orgId ).repo( repoId ).languages.get()
```

```javascript
assert.equal( languagesResponse.status, 200 )
```

List your notifications in a repository
List all notifications for the current user.

```javascript
notificationsResponse = client.repos.owner( orgId ).repo( repoId ).notifications.get({"all": true })
```

```javascript
assert.equal( notificationsResponse.status, 200 )
```

Mark notifications as read in a repository.
Marking all notifications in a repository as "read" removes them from the
default view on GitHub.com.

```javascript
putNotificationsResponse = client.repos.owner( orgId ).repo( repoId ).notifications.put({})
```

```javascript
assert.equal( putNotificationsResponse.status, 205 )
```

List assignees.
This call lists all the available assignees (owner + collaborators) to which
issues may be assigned.

```javascript
assigneesResponse = client.repos.owner( orgId ).repo( repoId ).assignees.get()
```

```javascript
assert.equal( assigneesResponse.status, 200 )
assignee = assigneesResponse.body[0].login
```

Check assignee.
You may also check to see if a particular user is an assignee for a repository.

```javascript
assigneeResponse = client.repos.owner( orgId ).repo( repoId ).assignees.assignee( assignee ).get()
```

```javascript
assert.equal( assigneeResponse.status, 204 )
```

Get list of branche

```javascript
branchesResponse = client.repos.owner( orgId ).repo( repoId ).branches.get()
```

```javascript
assert.equal( branchesResponse.status, 200 )
branchId = branchesResponse.body[0].name
```

Get Branche

```javascript
branchResponse = client.repos.owner( orgId ).repo( repoId ).branches.branch( branchId ).get()
```

```javascript
assert.equal( branchResponse.status, 200 )
```

List comments for a single commitList comments for a single commit

```javascript
commentsResponse = client.repos.owner( orgId ).repo( repoId ).commits.shaCode( commitShaCode ).comments.get()
```

```javascript
assert.equal( commentsResponse.status, 200 )
```

Create a label

```javascript
labelName = "API Notebook"
labelsResponse = client.repos.owner( orgId ).repo( repoId ).labels.post({
  "name": labelName,
  "color": "FFFFFF"
})
```

```javascript
assert.equal( labelsResponse.status, 201 )
```

List all labels for this repository

```javascript
labelsResponse = client.repos.owner( orgId ).repo( repoId ).labels.get()
```

```javascript
assert.equal( labelsResponse.status, 200 )
```

Get a single label

```javascript
labelNameResponse = client.repos.owner( orgId ).repo( repoId ).labels.name( labelName ).get()
```

```javascript
assert.equal( labelNameResponse.status, 200 )
```

Update a label

```javascript
patchLabelResponse = client.repos.owner( orgId ).repo( repoId ).labels.name( labelName ).patch({
  "name": labelName +" updated",
  "color": "DDDDDD"
})
```

```javascript
assert.equal( patchLabelResponse.status, 200 )
```

Delete a label

```javascript
newLabelName = patchLabelResponse.body.name
deleteLabelResponse = client.repos.owner( orgId ).repo( repoId ).labels.name( newLabelName ).delete()
```

```javascript
assert.equal( deleteLabelResponse.status, 204 )
```

Get the README.
This method returns the preferred README for a repository.

```javascript
readmeResponse = client.repos.owner( orgId ).repo( repoId ).readme.get()
```

```javascript
assert.equal( readmeResponse.status, 200 )
```

Get list of team

```javascript
teamsResponse = client.repos.owner( orgId ).repo( repoId ).teams.get()
```

```javascript
assert.equal( teamsResponse.status, 200 )
```

List watchers

```javascript
subscribersResponse = client.repos.owner( orgId ).repo( repoId ).subscribers.get()
```

```javascript
assert.equal( subscribersResponse.status, 200 )
```

Get list of contributors

```javascript
contributorsResponse = client.repos.owner( orgId ).repo( repoId ).contributors.get()
```

```javascript
assert.equal( contributorsResponse.status, 200 )
```

Get the weekly commit count for the repo owner and everyone else

```javascript
participationResponse = client.repos.owner( orgId ).repo( repoId ).stats.participation.get()
```

```javascript
assert( participationResponse.status == 200 || participationResponse.status == 202 )
```

Get the last year of commit activity data.
Returns the last year of commit activity grouped by week. The days array
is a group of commits per day, starting on Sunday.

```javascript
commitActivityResponse = client.repos.owner( orgId ).repo( repoId ).stats.commit_activity.get()
```

```javascript
assert( commitActivityResponse.status == 200 || commitActivityResponse.status == 202 )
```

Get contributors list with additions, deletions, and commit counts

```javascript
contributorsResponse = client.repos.owner( orgId ).repo( repoId ).stats.contributors.get()
```

```javascript
assert( contributorsResponse.status == 200 || contributorsResponse.status == 202 )
```

Get the number of commits per hour in each day.
Each array contains the day number, hour number, and number of commits
0-6 Sunday - Saturday
0-23 Hour of day
Number of commits

For example, [2, 14, 25] indicates that there were 25 total commits, during
the 2.00pm hour on Tuesdays. All times are based on the time zone of
individual commits.

```javascript
punchCardResponse = client.repos.owner( orgId ).repo( repoId ).stats.punch_card.get()
```

```javascript
assert.equal( punchCardResponse.status, 200 )
```

Get the number of additions and deletions per week.
Returns a weekly aggregate of the number of additions and deletions pushed
to a repository.

```javascript
codeFrequencyResponse = client.repos.owner( orgId ).repo( repoId ).stats.code_frequency.get()
```

```javascript
assert( codeFrequencyResponse.status == 200 || codeFrequencyResponse.status == 202 )
```

Delete a repository.

```javascript
deleteRepoResponse = client.repos.owner(orgId).repo(repoId).delete()
```

```javascript
assert.equal( deleteRepoResponse.status, 204 )
```