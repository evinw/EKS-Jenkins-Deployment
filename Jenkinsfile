pipeline {
    agent any

    environment {
        EKS_CLUSTER_NAME = "dev-eks-cluster"
        DOCKER_IMAGE = "dockerrepo/prodimage:latest"
        KUBECONFIG = "etc/kubeconfig"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'dev', url: 'https://github.com/evinw/app.git'
            }
        }
        stage('Code Quality Check') {
            steps {
               //SonarQube scanner
                sh 'sonar-scanner -Dsonar.projectKey=app -Dsonar.sources=. -Dsonar.host.url=http://sonarqube-server'
            }
        }
        stage('Build Docker Image') {
            steps {
                dir('docker') {
                    script {
                        sh "docker build -t $DOCKER_IMAGE ."
                        sh "trivy image $DOCKER_IMAGE"
                        sh "docker push $DOCKER_IMAGE"
                    }
                }
            }
        }
        stage('Run Tests') {
            steps {
                sh './gradlew test'
            }
        }
        stage('Deploy to EKS') {
            steps {
                script {
                    withAWS(credentials: 'aws-credentials') {
                        sh "aws eks update-kubeconfig --name $EKS_CLUSTER_NAME"
                    }
                    dir('k8s/dev') {
                        sh "kubectl apply -f deployment.yaml"
                        sh "kubectl apply -f service.yaml"
                    }
                }
            }
        }
    }

    post {
        success {
            echo 'Deployment was successful.'
        }
        failure {
            echo 'Deployment failed. Initiating rollback...'
            // rollback step
            // sh "kubectl rollout undo deployment/dev-deployment"
        }
        always {
            echo 'Deployment complete.'
        }
    }
}
