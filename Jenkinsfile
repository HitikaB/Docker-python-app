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
                    sh "docker build -t $registry:$BUILD_NUMBER"
                    }
                }
        }
        
        stage ("Push to ECR") {
            steps {
                script {
                    sh "sudo su"
                    sh "aws ecr get-login-password --region us-east-2 | docker login --username AWS --password-stdin 318988877498.dkr.ecr.us-east-2.amazonaws.com"
                    sh "docker push 318988877498.dkr.ecr.us-east-2.amazonaws.com/pyapp:$BUILD_NUMBER"
                }
            }
        }
        
        stage ("Deploy to K8S") {
            steps {
                withKubeConfig(caCertificate: '', clusterName: '', contextName: '', credentialsId: 'K8S', namespace: '', serverUrl: '') {
                      sh "sudo su - jenkins"
                      sh "kubectl apply -f HitikaB/Docker-app-python/pyapp-manifests"

                }
            }
         
        }
    } 
}
