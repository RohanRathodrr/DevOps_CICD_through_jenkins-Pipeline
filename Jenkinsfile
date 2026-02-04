pipeline {
    agent any

    tools {
        maven 'maven'
    }

    stages {

        stage('Build Maven') {
            steps {
                git 'https://github.com/RohanRathodrr/DevOps_CICD_through_jenkins-Pipeline.git'
                sh 'mvn clean install'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker buildx build -t rohanrathod/devops-integration .'
            }
        }

        stage('Push Image to Docker Hub') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-pwd',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh '''
                        echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                        docker push rohanrathod/devops-integration
                    '''
                }
            }
        }

        stage('EKS and Kubectl Configuration') {
            steps {
                sh 'aws eks update-kubeconfig --region ap-south-1 --name rohan-cluster'
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh 'kubectl apply -f deploymentservice.yaml'
            }
        }
    }
}
