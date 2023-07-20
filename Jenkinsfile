pipeline {
    agent any

    environment {
        registry = "public.ecr.aws/v9m7i5p8/pyapp"
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
                    sh "aws ecr-public get-login-password --region us-east-1 | docker login --username AWS --password-stdin public.ecr.aws/v9m7i5p8"
                    sh "docker push public.ecr.aws/v9m7i5p8/pyapp:latest"
                }
            }
        }
        
        stage ("Deploy to K8S") {
            steps {
                withKubeConfig(caCertificate: '', clusterName: '', contextName: '', credentialsId: 'K8S', namespace: '', serverUrl: '') {
                      sh "sudo su - jenkins"
                      sh "sudo su"
                      sh "kubectl create -f ingress-class.yaml"  
                      sh "kubectl create -f pyapp-deploy-eks.yml" 
                      sh "kubectl create -f ingress-pyapp.yaml"

                }
            }
         
        }
    } 
}
