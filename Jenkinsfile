pipeline {
    agent any

    environment {
        registry = "318988877498.dkr.ecr.us-east-2.amazonaws.com/pyapp"
    }
    stages {
        stage('checkout') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/HitikaB/Docker-python-app.git']]])
            }
        }
        
        stage ("build image") 
        {
            steps {
                script {
                    dockerImage = docker.build registry
                    sh "docker tag $registry:latest $registry:build-$BUILD_NUMBER"
                    }
                }
        }
        
        stage ("Push to ECR") {
            steps {
                script {
                    sh "sudo su"
                    sh "aws ecr get-login-password --region us-east-2 | docker login --username AWS --password-stdin 318988877498.dkr.ecr.us-east-2.amazonaws.com"
                    sh "docker push $registry:build-$BUILD_NUMBER"
                }
            }
        }

       stage('SSH and deployment') {
            steps {
                script {
                    sshPublisher(
                        continueOnError: false,
                        failOnError: true,
                        publishers: [sshPublisherDesc(configName: 'ec2private', transfers: [
                            sshTransfer(execCommand: "kubectl apply -f pyapp-manifests/", execTimeout: 120000)
                        ])]
                    )
                }
            }
        }
        
    } 
}
