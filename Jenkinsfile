pipeline{

    agent any 
    
    tools {
        maven 'maven'
    }

    stages{

        stage('Git Checkout'){
           
            steps{

                script{
                 
                 git branch: 'master', url: 'https://github.com/rameshkumarvermagithub/bank-finance-app.git'

                }
            }
        }
        stage('Unit Test'){

             steps{

              script{
                   
                   sh 'mvn test'

                }
             }
        }
        stage('Integration Test'){

             steps{

              script{
                   
                   sh 'mvn verify -DskipUnitTests'

                }
             }
        }
        stage('Static Code Analysis'){

             steps{

              script{
                   
                  withSonarQubeEnv(credentialsId: 'sonar') {
                     
                     sh 'mvn clean package sonar:sonar'
                  }
                }
             }
        }
        stage('Quality Gate status Check'){

             steps{

              script{
                   
                   waitForQualityGate abortPipeline: false, credentialsId: 'sonar'

                }
             }
        }
        stage('Maven Build'){

             steps{

              script{
                   
                   sh 'mvn clean install'

                }
             }
        }
        
        stage('Docker Build & Push') {
            steps {
                script{
                    withDockerRegistry(credentialsId: 'docker', toolName: 'docker') {
                        
                        sh "docker build -t rameshkumarverma/bank-finance-app ."
                        // sh "docker tag  bank-finance-app rameshkumarverma/bank-finance-app:latest"
                        sh "docker push rameshkumarverma/bank-finance-app:latest"
                    }
                }
            }
        }
        stage("TRIVY"){
            steps{
                sh "trivy image rameshkumarverma/bank-finance-app:latest > trivyimage.txt"
            }
        }
        stage("deploy_docker"){
            steps{
                sh "docker run -d --name bank-finance-app -p 8081:8081 rameshkumarverma/bank-finance-app:latest"
            }
        }

        stage('Deploy to kubernets'){
            steps{
                script{
                    // dir('K8S') {
                        withKubeConfig(caCertificate: '', clusterName: '', contextName: '', credentialsId: 'k8s', namespace: '', restrictKubeConfigAccess: false, serverUrl: '') {
                                sh 'kubectl apply -f deploymentservice.yml'
                                // sh 'kubectl apply -f service.yml'
                        }
                    // }
                }
            }
        }

    }
}
