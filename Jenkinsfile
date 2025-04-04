pipeline {
    
    agent any 
    
    stages {
        stage('Git Checkout'){
            steps{
                script{
                    git branch: 'main', url: 'https://github.com/karthidevops-sk/kubernetes-automation-java-project.git'
                    }
                }
            }
        stage('Maven build') {
            
            steps {
                
                script{
                    
                    sh 'mvn clean install'
                }
            }
        }
        stage('test') {
            steps {
                sh 'mvn test'
            }
        }
        stage('Docker image  build stage') {
            steps {
                sh 'docker image build -t $JOB_NAME:v.$BUILD_ID .'
            }
        }
        stage('Tag docker image') {
            steps {
                sh 'docker image tag $JOB_NAME:v.$BUILD_ID karthidevops01/$JOB_NAME:v.$BUILD_ID'
                }
        }
       stage ('push docker image to  dockerhub') {
            steps {
                script {
                   withCredentials([string(credentialsId: 'dockerhubpsd', variable: 'dockerpsw')]) {
                        sh '''
                        docker login -u karthidevops01 -p ${dockerpsw}
                        docker image push karthidevops01/$JOB_NAME:v.$BUILD_ID
                        docker rmi $JOB_NAME:v.$BUILD_ID
                        docker rmi karthidevops01/$JOB_NAME:v.$BUILD_ID
                        ''' 
                        }
                    }
                }
            }
        stage('deploy docker image') {
            steps {
                 sh '''
                 docker ps -q -f name=shopping-container && docker stop shopping-container && docker rm shopping-container || echo "Container not found or already stopped."
                 docker run -d -p 9191:8181 --name shopping-container karthidevops01/$JOB_NAME:v.$BUILD_ID
                 '''
            }
        }
    }
}
