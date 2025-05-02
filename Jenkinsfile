pipeline {
    
    agent any 
    
    stages {
        stage('Git Checkout'){
            steps{
                script{
                    git branch: 'SIT', url: 'https://github.com/khadar099/kubernetes-automation-java-project.git'
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
                sh 'docker image build -t shopping:v.$BUILD_NUMBER .'
            }
        }
        stage('Tag docker image') {
            steps {
                sh 'docker image tag shopping:v.$BUILD_NUMBER karthidevops01/shopping:v.$BUILD_NUMBER'
                }
        }
       stage ('push docker image to  dockerhub') {
            steps {
                script {
                   withCredentials([string(credentialsId: 'dockerhubpsd', variable: 'dockerpsw')]) {
                        sh '''
                        docker login -u karthidevops01 -p ${dockerpsw}
                        docker image push karthidevops01/shopping:v.$BUILD_NUMBER
                        docker rmi shopping:v.$BUILD_NUMBER
                        docker rmi karthidevops01/shopping:v.$BUILD_NUMBER
                        ''' 
                        }
                    }
                }
            }
    stage('Deploy to EKS') {
      steps {
        withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', credentialsId: 'aws-credentials']]) {
          sh '''
            echo "Configuring kubeconfig for EKS..."
            aws eks update-kubeconfig --region $ap-south-1 --name $mydemo_cluster
            echo "Deploying to EKS..."
            kubectl apply -f k8s/deployment-service.yaml.yaml
          '''
        }
      }
    
            }
        }
    }
}
