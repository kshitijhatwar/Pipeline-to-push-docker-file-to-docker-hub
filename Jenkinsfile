pipeline {
    agent any

    stages{

        stage("git login"){
            steps{
                git branch: 'main', url: 'https://github.com/kshitijhatwar/docker-jenkins.git'
            }
        }
        stage("docker Build"){
            steps{
                sh 'docker image build -t $JOB_NAME:v1.$BUILD_ID .'
                sh 'docker image tag $JOB_NAME:v1.$BUILD_ID kshitijhatwar/$JOB_NAME:v1.$BUILD_ID'
                sh 'docker image tag $JOB_NAME:v1.$BUILD_ID kshitijhatwar/$JOB_NAME:latest'
            }
        }
        stage("push docker image"){
            steps{
                withCredentials([string(credentialsId: 'docker-jenkins', variable: 'doc_jen')]) {
                sh "docker login -u kshitijhatwar -p ${doc_jen}"
                sh "docker image push kshitijhatwar/$JOB_NAME:v1.$BUILD_ID"
                sh "docker image push kshitijhatwar/$JOB_NAME:latest"
                sh "docker rmi $JOB_NAME:v1.$BUILD_ID kshitijhatwar/$JOB_NAME:v1.$BUILD_ID kshitijhatwar/$JOB_NAME:latest"
                }
            }
        }
        stage("Docker Container Deployment"){
            steps{
                script{
                    def docker_run = 'docker run -p 9008:80 --name docker-demo kshitijhatwar/dockerbuildjob:latest'
                    def docker_rmv_container = 'docker rm -f docker-demo'
                    def docker_rmi = 'docker rmi -f kshitijhatwar/dockerbuildjob'

                    sshagent(['sshKey']){
                        sh "ssh -o StrictHostKeyChecking=no ubuntu@13.201.18.4 ${docker_rmv_container}"
                        sh "ssh -o StrictHostKeyChecking=no ubuntu@13.201.18.4 ${docker_rmi}"
                        sh "ssh -o StrictHostKeyChecking=no ubuntu@13.201.18.4 ${docker_run}"
                    }
                }
            }
        }
    }
}