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
                dir("${env.WORKSPACE}/spring"){
                sh 'mvn clean install'
                sh 'ls -l target/'
              }
            }
        }
    }
    post {
        always {
            archiveArtifacts artifacts: "spring/target/**/*.jar", fingerprint: true
        }
    }
}
