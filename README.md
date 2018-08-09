# Jenkins Continuous Integration Test
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

## Automated Testing

Automated testing of the build is implemented with Mocha.
If the script is defined in the _package.json_ as
```json
"scripts": {
    "test": "mocha"
  },
```
the testing process can be called by adding
```sh
npm test
```
to the _Execute shell_ step in the _Build_ section of the Jenkins configuration.

## Jenkins Pipeline

Jenkins allows the creation of a pipeline item which performs tasks based off a script instead of directly specifying shell commands.
This has the advantage, that the script (_Jenkinsfile_) can also be included in the repository and referenced from Jenkins, thus creating a central source of truth for the building steps.

The sample _Jenkinsfile_ for building and testing which specifies the shell commands in the _Build_ and _Tests_ stages:
```groovy
#!/usr/bin/env groovy

pipeline {

    agent any

    stages {
        stage('Build') {
            steps {
                echo 'Building...'
                sh 'npm install'
            }
        }
        stage('Test') {
            steps {
                echo 'Testing...'
                sh 'npm test'
            }
        }
    }
}
```
When configuring the pipeline item in Jenkins select the _pipeline script from SCM_ option in the _Pipeline_ section and specify the matching path to the _Jenkinsfile_.




## Common Errors

Don't forget the trailing slash when specifying the _payload URL_.
