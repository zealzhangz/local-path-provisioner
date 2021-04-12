pipeline {
  agent {
    node {
      label 'go'
    }
  }

    environment {
        DOCKER_CREDENTIAL_ID = 'dockerhub-id'
        REGISTRY = '192.168.0.162'
        HARBOR_NAMESPACE = 'tsdb'
    }

    stages {
        stage ('checkout scm') {
            steps {
                container('maven') {
                  git url: 'git@github.com:zealzhangz/local-path-provisioner.git'
                }
            }
        }

        stage ('build & push') {
            steps {
                container ('go') {
                    #sh 'go build'
                    sh 'docker build -f `pwd`/package/Dockerfile -t $REGISTRY/$HARBOR_NAMESPACE/local-path-provisioner:v0.20.$BUILD_NUMBER .'
                    withCredentials([usernamePassword(passwordVariable : 'DOCKER_PASSWORD' ,usernameVariable : 'DOCKER_USERNAME' ,credentialsId : "$DOCKER_CREDENTIAL_ID" ,)]) {
                        sh 'echo "$DOCKER_PASSWORD" | docker login $REGISTRY -u "$DOCKER_USERNAME" --password-stdin'
                        sh 'docker push  $REGISTRY/$HARBOR_NAMESPACE/local-path-provisioner:v0.20.$BUILD_NUMBER'
                    }
                }
            }
        }

        stage('push latest'){
           steps{
                container ('maven') {
                  sh 'docker tag  $REGISTRY/$HARBOR_NAMESPACE/local-path-provisioner:v0.20.$BUILD_NUMBER $REGISTRY/$HARBOR_NAMESPACE/local-path-provisioner:latest '
                  sh 'docker push  $REGISTRY/$HARBOR_NAMESPACE/local-path-provisioner:latest '
                }
           }
        }
    }
}