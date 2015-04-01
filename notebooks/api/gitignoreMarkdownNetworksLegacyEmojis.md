---
site: https://anypoint.mulesoft.com/apiplatform/popular/admin/#/dashboard/apis/7782/versions/7918/portal/pages/6522/edit
apiNotebookVersion: 1.1.66
title: Gitignore, markdown, networks, legacy, emojis
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

Listing available templates.
List all templates available to pass as an option when creating a repository.

```javascript
gitIgnoreTemplatesResponse = client.gitignore.templates.get()
```

```javascript
assert.equal( gitIgnoreTemplatesResponse.status, 200 )
```

Get a single template

```javascript
languageResponse = client.gitignore.templates.language( "C" ).get()
```

```javascript
assert.equal( languageResponse.status, 200 )
```

Render an arbitrary Markdown document

```javascript
postMarkdownResponse = client.markdown.post({
  "text" : "Hello world github/linguist#1 **cool**, and #1!" ,
  "mode" : "gfm" ,
  "context" : "github/gollum"
})
```

```javascript
assert.equal( postMarkdownResponse.status, 200 )
```

Render a Markdown document in raw mode

```javascript
rawResponse = client.markdown.raw.post("codeblock\n``` javascript\nvar a = 10\n```",{headers:{"Content-Type":"text/plain"}})
```

```javascript
assert.equal( rawResponse.status, 200 )
```

Let's pick some repository.

```javascript
publicRepoResponse = client.repositories.get()
repoId = publicRepoResponse.body[0].name
ownerId = publicRepoResponse.body[0].owner.login
```

List public events for a network of repositories.

```javascript
networksEventsResponse = client.networks.owner( ownerId ).repo( repoId ).events.get()
```

```javascript
assert.equal( networksEventsResponse.status, 200 )
```

Find issues by state and keyword

```javascript
state = "open"
keyword = "bug"
issuesSearchResponse = client.legacy.issues.search.owner( ownerId ).repository( repoId ).state( state ).keyword( keyword ).get()
```

```javascript
assert.equal( issuesSearchResponse.status, 200 )
```

Find repositories by keyword. Note, this legacy method does not follow the v3 pagination pattern. This method returns up to 100 results per page and pages can be fetched using the start_page parameter

```javascript
keyword = "RAML"
reposSearchKeywordResponse = client.legacy.repos.search.keyword( keyword ).get()
```

```javascript
assert.equal( reposSearchKeywordResponse.status, 200 )
```

Find users by keyword

```javascript
keyword = "api-notebook-user"
userSearchKeywordResponse = client.legacy.user.search.keyword( keyword ).get()
```

```javascript
assert.equal( userSearchKeywordResponse.status, 200 )
```

This API call is added for compatibility reasons only

```javascript
userEmailResponse = client.legacy.user.email.email( "api.notebook.user@somehost.com" ).get()
```

```javascript
assert.equal( userEmailResponse.status, 200 )
```

Lists all the emojis available to use on GitHub

```javascript
emojisResponse = client.emojis.get()
```

```javascript
assert.equal( emojisResponse.status, 200 )
```