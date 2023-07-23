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
                    }
                }
        }
        
        stage ("Push to ECR") {
            steps {
                script {
                    sh "sudo su"
                    sh "aws ecr get-login-password --region us-east-2 | docker login --username AWS --password-stdin 318988877498.dkr.ecr.us-east-2.amazonaws.com"
                    sh "docker push 318988877498.dkr.ecr.us-east-2.amazonaws.com/pyapp:latest"
                    sh "docker tag $registry:latest $registry:build-$BUILD_NUMBER"
                    sh "docker push $registry:build-$BUILD_NUMBER"
                }
            }
        }
       stage ("Deploy to K8S") {
            steps {
                script {
                    sh "kubectl apply -f pyapp-manifests/"

                }
            }
         
        } 
}
}
