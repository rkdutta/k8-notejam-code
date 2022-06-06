// Uses Declarative syntax to run commands inside a container.
pipeline {
    agent {
        kubernetes {
            yaml '''
apiVersion: v1
kind: Pod
spec:
  containers:
  - name: maven-build-agent
    image: container-registry:5000/private-jdk-alpine
    command:
    - sleep
    args:
    - 600
            '''
            defaultContainer 'maven-build-agent'
        }
    }
    // triggers {
    //     pollSCM('* * * * *')
    // }
    stages {
        stage('Build') {
            steps {
              sh 'ls -l'
              sh 'cd ./spring/'
              sh 'ls -'
              sh 'mvn clean install'
            }
        }
    }
}
