pipeline {
    agent any

    stages {
        stage('Go to Git') {
            steps {
                // This stage is to checkout the Git Repo
                checkout scm
            }
        }
        stage('Build and Push') {
            steps {
                script {
                    // Defining Docker Image and Tag variables
                    def dockerImage = "aryansr/jenkins-covid-app"
                    def dockerTag = "v3"
                    def dockerCredentialsId = "DockerHub"
                    // Starting the Build Process
                    def dockerBuild = docker.build("${dockerImage}:${dockerTag}",'.')
                    docker.withRegistry('', dockerCredentialsId) {
                        dockerBuild.push()
                    }
                }
            }
        }
        stage('Deploy') {
            agent { label 'kubenode' }
            steps {
                script {
                    withKubeConfig([credentialsId: 'azure-kube', serverUrl: 'https://networknuts-dns-iykqvh7w.hcp.centralindia.azmk8s.io']) {
                        sh 'kubectl create -f deployment.yml'
                }
            }
        }
    }
}
}
