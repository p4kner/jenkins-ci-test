# Jenkins Continuous Integration Test
Testing continuous integration using Jenkins with node.js Amazon Alexa Skill.

Jenkins is an automation server for tasks such as building, testing, and deploying software projects. Once Jenkins has been installed this project which inludes code for a simple Amazon Alexa endpoint can be used to demonstrate

* Obtaining project source code from Git repository
* Automatically triggering build based on Github webhook or polling a repository for changes
* Performing build tasks such as executing shell commands
* Deploying up and running project to desired location.

## Node.js Tasks

It is not sufficient to merely clone a Git repository to obtain a working node.js project. The node modules need to be installed and automated testing has been added (so that Jenkins has more to do).

### Installing Node Modules

Node modules specified in the _package.json_ are installed by calling this shell command:

```sh
npm install
```

### Automated Testing

Automated testing of the build is implemented with Mocha.
If the script is defined in the _package.json_ as
```json
"scripts": {
    "test": "mocha"
  },
```
the testing process can be called with the shell command:
```sh
npm test
```
to the _Execute shell_ step in the _Build_ section of the Jenkins configuration.

## Configuring Jenkins

Most of the configuration for Jenkins has to be performed in the Jenkins backend.

### Plug-ins

A lot of the desired functionality of Jenkins is achieved by utilising plug-ins.

### Jenkins Items

### Jenkins Pipeline

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

### Github Authentication

When accessing a private Github repository from Jenkins it is necessary to authenticate via SSH key.

The following shell commands can be used to generate the ssh key for the email address of the Github user.
Please make sure to leave file name and passwords blank as Jenkins doesn't enter a password.
```sh
sudo su
whoami
# root
sudo su jenkins
whoami
# jenkins
ssh-keygen -t rsa -b 4096 -C "sample@mail.com"
# Github user email address. Leave filename and password blank.
```
Copy the public ssh key and paste it into the _SSH and GPG keys_ section of the Github _Personal settings_.
```sh
cd /var/lib/jenkins/.ssh
vi id_rsa.pub
# Open file to copy.
```
Verify access to Github over SSH which handily adds github.com to known hosts.
```sh
ssh -T git@github.com
# The authenticity of host 'github.com (192.30.253.112)' can't be established...
```

### Github Webhook

It is desirable to trigger builds on new git pushes.
One possibility for achieving this is using a github webhook which notifies Jenkins running on the server.
The webhook can be created in the _settings_ section of the Github repository, where a _payload URL_ has to be specified.

Payload URL format:
```
https://<jenkins ip>:<jenkins port>/github-webhook/
```

Example Payload URLs:
```
https://212.60.203.18:8080/github-webhook/
https://dev.example.com:8080/github-webhook/
```
The Jenkins item can be configured to use this webhook in the _Build Triggers_ section by selecting the _GitHub hook trigger for GITScm polling_ option.








## Common Errors

Don't forget the trailing slash when specifying the _payload URL_.
