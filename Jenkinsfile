pipeline {
    agent any
    environment {
        DOCKER_HUB_REPO = "irfanahmed379/irfan"
        CONTAINER_NAME = "flask-container"
        STUB_VALUE = "200"
    }
    stages {
        stage('Stubs-Replacement'){
            steps {
                // 'STUB_VALUE' Environment Variable declared in Jenkins Configuration 
                echo "STUB_VALUE = ${STUB_VALUE}"
                sh "sed -i 's/<STUB_VALUE>/$STUB_VALUE/g' config.py"
                sh 'cat config.py'
            }
        }
        stage('Build') {
            steps {
                //  Building new image
                sh 'docker image build -t $DOCKER_HUB_REPO:4 .'
                sh 'docker image tag $DOCKER_HUB_REPO:4 $DOCKER_HUB_REPO:$BUILD_NUMBER'

                //  Pushing Image to Repository
                withDockerRegistry([ irfanahmed379: "irfanahmed379", https://hub.docker.com/: "" ])
                bat "docker push irfanahmed379/irfan:build"
                //sh 'docker push irfanahmed379/irfan:$BUILD_NUMBER'
                //sh 'docker push irfanahmed379/irfan:4'
                
                echo "Image built and pushed to repository"
            }
        }
        stage('Deploy') {
            steps {
                script{
                    //sh 'BUILD_NUMBER = ${BUILD_NUMBER}'
                    if (BUILD_NUMBER == "1") {
                        sh 'docker run --name $CONTAINER_NAME -d -p 5000:5000 $DOCKER_HUB_REPO'
                    }
                    else {
                        sh 'docker stop $CONTAINER_NAME'
                        sh 'docker rm $CONTAINER_NAME'
                        sh 'docker run --name $CONTAINER_NAME -d -p 5000:5000 $DOCKER_HUB_REPO'
                    }
                    //sh 'echo "Latest image/code deployed"'
                }
            }
        }
    }
}
