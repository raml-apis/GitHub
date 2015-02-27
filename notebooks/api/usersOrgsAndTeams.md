---
site: https://anypoint.mulesoft.com/apiplatform/popular/admin/#/dashboard/apis/7782/versions/7918/portal/pages/6520/preview
apiNotebookVersion: 1.1.66
title: Users, Orgs and Teams
---

```javascript
load('https://github.com/chaijs/chai/releases/download/1.9.0/chai.js')
```

See http://chaijs.com/guide/styles/ for assertion styles

```javascript
assert = chai.assert
```

```javascript
notebookUserId = prompt("Please, enter some GitHub user ID.\n\nThis User will be added to your organization and team and removed from them later on.\n\nDo not enter your own ID as you can not be removed from your own organization.\n\nRemember, that you must have access to the users email.")
```

Enter ID of organization which is operated by the notebook.
**Attention** the organization should be created manually before the notebook is launched.

```javascript
orgId = prompt("Please, enter ID of your GitHub test organization.")
```

Name and Id of repository which is operated by the notebook.

```javascript
repoName = "API Notebook Test Organization Repository 8"
repoId = "API-Notebook-Test-Organization-Repository-8"
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

Get all users.
This provides a dump of every user, in the order that they signed up for GitHub.
Note: Pagination is powered exclusively by the since parameter. Use the Link
header to get the URL for the next page of users.

```javascript
usersResponse = client.users.get()
```

```javascript
assert.equal( usersResponse.status, 200 )
```

Let's retreive the authorized user.

```javascript
currentUserResponse = client.user.get()
currentUserId = currentUserResponse.body.login
```

Get a single user

```javascript
userResponse = client.users.userId( currentUserId ).get()
```

```javascript
assert.equal( userResponse.status, 200 )
```

List public repositories for the specified user

```javascript
reposResponse = client.users.userId( currentUserId ).repos.get()
```

```javascript
assert.equal( reposResponse.status, 200 )
```

List all public organizations for a user

```javascript
orgsResponse = client.users.userId( currentUserId ).orgs.get()
```

```javascript
assert.equal( orgsResponse.status, 200 )
```

List a users gists

```javascript
gistsResponse = client.users.userId( currentUserId ).gists.get()
```

```javascript
assert.equal( gistsResponse.status, 200 )
```

Let's follow the api notebook test user, so that the subsequent check would have positive result.  

```javascript
putFollowResponse = client.user.following.userId( notebookUserId ).put()
```

Check if one user follows another

```javascript
followingResponse = client.users.userId( currentUserId ).following.targetUserId( notebookUserId ).get()
```

```javascript
assert.equal( followingResponse.status, 204 )
```

Now let's unfollow the api notebook test user.

```javascript
deleteFollowResponse = client.user.following.userId( notebookUserId ).delete()
```

List public keys for a user.
Lists the verified public keys for a user. This is accessible by anyone.

```javascript
keysResponse = client.users.userId( currentUserId ).keys.get()
```

```javascript
assert.equal( keysResponse.status, 200 )
```

If you are authenticated as the given user, you will see your private events. Otherwise, you'll only see public events

```javascript
eventsResponse = client.users.userId( currentUserId ).events.get()
```

```javascript
assert.equal( eventsResponse.status, 200 )
```

This is the user's organization dashboard. You must be authenticated as the user to view this

```javascript
currentUserOrgResponse = client.user.orgs.get()
```

```javascript
assert.equal( currentUserOrgResponse.status, 200 )
```

This is the user's organization dashboard. You must be authenticated as the user to view this.

```javascript
orgEventResponse = client.users.userId( currentUserId ).events.orgs.org( orgId ).get()
```

```javascript
assert.equal( orgEventResponse.status, 200 )
```

These are events that you'll only see public events

```javascript
received_eventsResponse = client.users.userId( currentUserId ).received_events.get()
```

```javascript
assert.equal( received_eventsResponse.status, 200 )
```

List public events that a user has receive

```javascript
received_eventsResponse = client.users.userId( currentUserId ).received_events.public.get()
```

```javascript
assert.equal( received_eventsResponse.status, 200 )
```

List repositories being starred by a user

```javascript
starredResponse = client.users.userId( currentUserId ).starred.get()
```

```javascript
assert.equal( starredResponse.status, 200 )
```

List repositories being watched by a user

```javascript
subscriptionsResponse = client.users.userId( currentUserId ).subscriptions.get()
```

```javascript
assert.equal( subscriptionsResponse.status, 200 )
```

List a user's follower

```javascript
followersResponse = client.users.userId( currentUserId ).followers.get()
```

```javascript
assert.equal( followersResponse.status, 200 )
```

Get an Organization

```javascript
orgResponse = client.orgs.orgId( orgId ).get()
```

```javascript
assert.equal( orgResponse.status, 200 )
```

Edit an Organization

```javascript
orgUpdateResponse = client.orgs.orgId( orgId ).patch({
  "name": orgId
})
```

```javascript
assert.equal( orgUpdateResponse.status, 200 )
```

Members list.
List all users who are members of an organization. A member is a user tha
belongs to at least 1 team in the organization. If the authenticated user
is also an owner of this organization then both concealed and public members
will be returned. If the requester is not an owner of the organization the
query will be redirected to the public members list.

```javascript
membersResponse = client.orgs.orgId( orgId ).members.get()
```

```javascript
assert.equal( membersResponse.status, 200 )
```

Check if a user is, publicly or privately, a member of the organization

```javascript
memberResponse = client.orgs.orgId( orgId ).members.userId( currentUserId ).get()
```

```javascript
assert.equal( memberResponse.status, 204 )
```

List repositories for the specified org

```javascript
reposResponse = client.orgs.orgId( orgId ).repos.get()
```

```javascript
assert.equal( reposResponse.status, 200 )
```

Let's delete a repository which could have been created during earlier notebook runs.

```javascript
client.repos.ownerId( orgId ).repoId( repoId ).delete()
```

This is the user’s organization dashboard. You must be authenticated as the user to view this.

```javascript
postReposResponse = client.orgs.orgId( orgId ).repos.post({
  "name": repoName
})
```

```javascript
assert.equal( postReposResponse.status, 201 )
```

List teams

```javascript
teamsResponse = client.orgs.orgId( orgId ).teams.get()
```

```javascript
assert.equal( teamsResponse.status, 200 )
```

Let's delete a team which could have been created during earlier notebook runs.

```javascript
for(var ind in teamsResponse.body){
  var team = teamsResponse.body[ind]
  if(team.name=="Test Notebook Team"){
    deleteTeamResponse = client.teams.teamsId( team.id ).delete()
    break
  }
}
```

Create team. In order to create a team, the authenticated user must be an owner of orgId.

```javascript
postTeamsResponse = client.orgs.orgId( orgId ).teams.post({
  "name": "Test Notebook Team",
  "repo_names": [ repoName ],
  "permission": "pull"
})
```

```javascript
assert.equal( postTeamsResponse.status, 201 )
teamId = postTeamsResponse.body.id
```

Publicize a user's membership

```javascript
putPublic_membersResponse = client.orgs.orgId( orgId ).public_members.userId( currentUserId ).put()
```

```javascript
assert.equal( putPublic_membersResponse.status, 204 )
```

Public members list.
Members of an organization can choose to have their membership publicized
or not.

```javascript
publicMembersResponse = client.orgs.orgId( orgId ).public_members.get()
```

```javascript
assert.equal( publicMembersResponse.status, 200 )
```

Check public membership

```javascript
checkPublicMemberResponse = client.orgs.orgId( orgId ).public_members.userId( currentUserId ).get()
```

```javascript
assert.equal( checkPublicMemberResponse.status, 204 )
```

Conceal a user's membership

```javascript
deletePublicMemberResponse = client.orgs.orgId( orgId ).public_members.userId( currentUserId ).delete()
```

```javascript
assert.equal( deletePublicMemberResponse.status, 204 )
```

List public events for an organization

```javascript
eventsResponse = client.orgs.orgId( orgId ).events.get()
```

```javascript
assert.equal( eventsResponse.status, 200 )
```

List issues.
List all issues for a given organization for the authenticated user.

```javascript
issuesResponse = client.orgs.orgId( orgId ).issues.get({
  "filter": "all",
  "sort": "created",
})
```

```javascript
assert.equal( issuesResponse.status, 200 )
```

Get team

```javascript
teamResponse = client.teams.teamsId( teamId ).get()
```

```javascript
assert.equal( teamResponse.status, 200 )
```

Edit team.
In order to edit a team, the authenticated user must be an owner of the org
that the team is associated with.

```javascript
patchTeamResponse = client.teams.teamsId( teamId ).patch({
  "name": teamResponse.body.name + " Updated",
  "permission": "admin"
})
```

```javascript
assert.equal( patchTeamResponse.status, 200 )
```



Add team membership.
In order to add a membership between a user and a team, the authenticated user must have ‘admin’ permissions to the team or be an owner of the organization that the team is associated with.

If the user is already a part of the team’s organization (meaning they’re on at least one other team in the organization), this endpoint will add the user to the team.

If the user is completely unaffiliated with the team’s organization (meaning they’re on none of the organization’s teams), this endpoint will send an invitation to the user via email. This newly-created membership will be in the “pending” state until the user accepts the invitation, at which point the membership will transition to the “active” state and the user will be added as a member of the team.

```javascript
addTeamMemberResponse = client.teams.teamsId( teamId ).memberships.userId( notebookUserId ).put()
```

```javascript
assert.equal( addTeamMemberResponse.status, 200 )
```

List team members.
In order to list members in a team, the authenticated user must be a member
of the team.

```javascript
teamMembersResponse = client.teams.teamsId( teamId ).members.get()
```

```javascript
assert.equal( teamMembersResponse.status, 200 )
```

Get team membership.
In order to get a user’s membership with a team, the authenticated user must be a member of the team or an owner of the team’s organization.

```javascript
teamMemberResponse = client.teams.teamsId( teamId ).memberships.userId( notebookUserId ).get()
```

```javascript
assert.equal( teamMemberResponse.status, 200 )
```

Remove team membership.
In order to remove a membership between a user and a team, the authenticated user must have ‘admin’ permissions to the team or be an owner of the organization that the team is associated with. NOTE: This does not delete the user, it just removes their membership from the team.

```javascript
deleteTeamMemberResponse = client.teams.teamsId( teamId ).memberships.userId( notebookUserId ).delete()
```

```javascript
assert.equal( deleteTeamMemberResponse.status, 204 )
```

List team repos

```javascript
reposResponse = client.teams.teamsId( teamId ).repos.get()
```

```javascript
assert.equal( reposResponse.status, 200 )
```

In order to remove a repository from a team, the authenticated user must be an owner of the org that the team is associated with. NOTE: This does not delete the repository, it just removes it from the team

```javascript
deleteManagerResponse = client.teams.teamsId( teamId ).repos.ownerId( orgId ).repoId( repoId ).delete()
```

```javascript
assert.equal( deleteManagerResponse.status, 204 )
```

In order to add a repository to a team, the authenticated user must be an owner of the org that the team is associated with. Also, the repository must be owned by the organization, or a direct fork of a repository owned by the organization

```javascript
putManagerResponse = client.teams.teamsId( teamId ).repos.orgId( orgId ).repoId( repoId ).put()
```

```javascript
assert.equal( putManagerResponse.status, 204 )
```

Check if a team manages a repositor

```javascript
 managesRepoResponse = client.teams.teamsId( teamId ).repos.ownerId( orgId ).repoId( repoId ).get()
```

```javascript
assert.equal( managesRepoResponse.status, 204 )
```

Add the user to the team once again

```javascript
addTeamMemberResponse = client.teams.teamsId( teamId ).memberships.userId( notebookUserId ).put()
```

```javascript
window.alert("At this point you must check email of " + notebookUserId + " and confirm team membersip.")
```

Remove a member.
Removing a user from this list will remove them from all teams and they
will no longer have any access to the organization's repositories.

```javascript
deleteMemberResponse = client.orgs.orgId( orgId ).members.userId( notebookUserId ).delete()
```

```javascript
assert.equal( deleteMemberResponse.status, 204 )
```

Delete team.
In order to delete a team, the authenticated user must be an owner of the
org that the team is associated with.

```javascript
deleteTeamResponse = client.teams.teamsId( teamId ).delete()
```

```javascript
assert.equal( deleteTeamResponse.status, 204 )
```

Garbage collection. Delete a repository.

```javascript
deleteRepResponse = client.repos.ownerId( orgId ).repoId( repoId ).delete()
```