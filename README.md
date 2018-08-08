# jenkins-ci-test
Testing continuous integration using Jenkins with node.js Amazon Alexa Skill.

## Github Webhook

It is desirable to have automated builds on new git pushes.
One possibility for achieving this is using a github webhook which notifies Jenkins running on the server.
The webhook can be created in the _settings_ section of the Github repository, where a _payload URL_ has to be specified.

Payload URL format:
```
https://<jenkins ip>:<jenkins port>/github-webhook/
```

Example Payload URLs:
```
https://212.60.203.18:8080/gitub-webhook/
https://dev.example.com/gitub-webhook/
```



## Common Errors

Don't forget the trailing slash when specifying the _payload URL_.
