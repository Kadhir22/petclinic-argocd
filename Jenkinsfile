pipeline {
    agent { label 'cicd' }

    stages {
        stage('Cloning frontend') {
            steps {
               git branch: 'master', credentialsId: 'githu-cred', url: 'https://github.com/Kadhir22/petclinic-spring-petclinic-angular.git'
            }
        }
        stage('Cloning backend') {
            steps {
               git branch: 'master', credentialsId: 'githu-cred', url: 'https://github.com/Kadhir22/spring-petclinic-rest.git'
            }
        }

        stage('Containerization frontend') {
            steps {
                sh 'cd /home/jenkins/workspace/petclinic-spring-petclinic-angular'
                withCredentials([usernamePassword(
                    credentialsId: 'dochub-cred',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh 'docker build -t frontend:4 .'
                    sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                    sh 'docker tag frontend:4 kadhir22/angular-petclinic:4'
                    sh 'docker push kadhir22/angular-petclinic:4'
                    sh 'docker rmi kadhir22/angular-petclinic:4'
                }
            }
        }
        stage('Cleanup Workspace') {
            steps {
                cleanWs()
            }
        }
        stage('Containerization backend') {
            steps {
                    sh 'cd /home/jenkins/workspace/spring-petclinic-rest'
                    sh 'docker build -t backend:4 .'
                    sh 'docker tag backend:4 kadhir22/petclinic-minikube:4'
                    sh 'docker push kadhir22/petclinic-minikube:4'
                    sh 'docker rmi kadhir22/petclinic-minikube:4'
                
            }
        }

        stage('Cleanup Workspace') {
            steps {
                cleanWs()
            }
        }

        stage('K8s Deployment') {
            steps {
                sh 'kubectl rollout restart deployment angular-frontend'
                sh 'kubectl rollout restart deployment petclinic-backend'
            }
        }
    }
}
