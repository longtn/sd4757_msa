pipeline {
    agent any
    options {
        skipStagesAfterUnstable()
    }
    stages {
         stage('Clone repository') { 
            steps { 
                script{
                    checkout scm
                }
            }
        }

        stage('Build FE') { 
            steps { 
                script{
                 frontend = docker.build("ecr-fe", "-f Dockerfile src/frontend")
                }
            }
        }
        stage('Push FE') {
            steps {
                script{
                        docker.withRegistry('191627991384.dkr.ecr.ap-southeast-1.amazonaws.com/ecr-fe', 'ecr:ap-southeast-1:aws-credentials') {
                        frontend.push("latest")
                    }
                }
            }
        }

        stage('Deploy FE'){
            steps {
                 sh 'kubectl apply -f frontend.yaml'
            }
        }

        stage('Build BE') { 
            steps { 
                script{
                 frontend = docker.build("ecr-be", "-f Dockerfile src/backend")
                }
            }
        }
        stage('Push BE') {
            steps {
                script{
                        docker.withRegistry('https://191627991384.dkr.ecr.ap-southeast-1.amazonaws.com/ecr-be', 'ecr:ap-southeast-1:aws-credentials') {
                        frontend.push("latest")
                    }
                }
            }
        }

        stage('Deploy BE'){
            steps {
                 sh 'kubectl apply -f backend.yaml'
            }
        }

        stage('Deploy mongo'){
            steps {
                script {
                    sh 'kubectl apply -f mongodb.yaml'
                }
            }
        }

        stage('Deploy nginx'){
            steps {
                 script {
                    sh 'kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.7.1/deploy/static/provider/cloud/deploy.yaml'
                    sh 'kubectl apply -f ingress.yml'
                 }
            }
        }
    }
}