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
                  image: container-registry:5000/private-jdk-alpine:latest
                  command:
                  - sleep
                  args:
                  - 600
                  volumeMounts:
                  - mountPath: "/etc/docker/certs.d/container-registry.04-container-registry.svc.cluster.local:5000/"
                    name: ca-cert
                    readOnly: true
                  - mountPath: /var/run/docker.sock
                    name: docker-sock
                volumes:
                 - name: ca-cert
                   secret:
                     secretName: certs-secret-ca
                     optional: false
                 - name: docker-sock
                   hostPath:
                      path: /var/run/docker.sock
            '''
            defaultContainer 'maven-build-agent'
        }
    }

    stages {
        stage('Build') {
          environment {
               CONTAINER_REGISTRY_CREDS = credentials('container-registry-docker-login-creds')
           }
            steps {
                //sh "docker login container-registry:5000 -u ${CONTAINER_REGISTRY_CREDS_USR} -p ${CONTAINER_REGISTRY_CREDS_PSW}"
                sh 'docker login container-registry:5000 -u $CONTAINER_REGISTRY_CREDS_USR -p $CONTAINER_REGISTRY_CREDS_PSW'
                dir("${env.WORKSPACE}/spring"){
                sh 'mvn clean install'
              }
            }
        }
        stage('Publish') {
          steps {
              archiveArtifacts artifacts: "spring/target/**/*.jar", fingerprint: true
            }
        }
        stage('Push to Container Repo') {
          environment {
               CONTAINER_REGISTRY_CREDS = credentials('container-registry-docker-login-creds')
           }
          steps {
                 sh "docker login container-registry:5000 -u ${CONTAINER_REGISTRY_CREDS_USR} -p ${CONTAINER_REGISTRY_CREDS_PSW}"
                 dir("${env.WORKSPACE}/spring"){
                   sh "docker build -t container-registry:5000/private-notejam:${env.BUILD_ID} ."
                   sh "docker push container-registry:5000/private-notejam:${env.BUILD_ID}"
                 }
            }
        }
    }
}
