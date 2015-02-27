Configuring GitHub connector

- In order to run this connector you will need a GitHub account.
- You must also register a GitHub application at [https://github.com/settings/applications/new](https://github.com/settings/applications/new).
- Do not forget to set redirect URI for the application. In our case Mule application is launched locally and the redirect URI belongs to the localhost domain: http://localhost:9000/codeGithub
- Specify Client Id, Client Secret and redirect URI of your application in the Authentication tab of the HTTP Request Configuration dialog. Alternatively, you may set corresponding attributes right in the XML file.


Running GitHub connector

- Make a call on your Local Authorization URL which is http://localhost:9000/authorizeGithub. You will be prompted to authorize your application.
- Make a call on the http://localhost:8081/github URL.




Configuring GitHub Upload connector

- In order to run this connector you will need a GitHub account.
- You must also register a GitHub application at [https://github.com/settings/applications/new](https://github.com/settings/applications/new).
- Do not forget to set redirect URI for the application. In our case Mule application is launched locally and the redirect URI belongs to the localhost domain: http://localhost:9000/codeGithubUpload
- Specify Client Id, Client Secret and redirect URI of your application in the Authentication tab of the HTTP Request Configuration dialog. Alternatively, you may set corresponding attributes right in the XML file.
- The GitHub Upload API has a single method which requires a nonempty JSON payload. In this example payload is attached by means of Java transformer which is prueba.GitHubUploadTransformer. You must either have this class on your classpath or provide payload in some other way.
- In your request you must pass your GitHub username in the 'userId' query parameter.
- In your request you must pass name of your repository in the 'repoId' query parameter.
- In your request you must pass ID of release of the repository above in the 'releaseId' query parameter. [Here](https://help.github.com/articles/creating-releases/) you may learn how to make a release. The complete list of releases (and their IDs) is provided by GET https://api.github.com/repos/{userId}/{repoId}/releases request.


Running GitHub Upload connector

- Make a call on your Local Authorization URL which is http://localhost:9000/authorizeGithubUpload. You will be prompted to authorize your application.
- Make a call on the http://localhost:8081/githubUpload URL.