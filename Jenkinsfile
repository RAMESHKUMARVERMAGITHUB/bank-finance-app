pipeline{

    agent any 
    
    tools {
        maven 'maven'
    }

    stages{

        stage('Git Checkout'){
           
            steps{

                script{
                 
                 git branch: 'master', url: 'https://github.com/rameshkumarvermagithub/spock-lizard-docker.git'

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
                        
                        sh "docker build -t rameshkumarverma/spock-lizard-docker ."
                        // sh "docker tag  spock-lizard-docker rameshkumarverma/spock-lizard-docker:latest"
                        sh "docker push rameshkumarverma/spock-lizard-docker:latest"
                    }
                }
            }
        }
        stage("TRIVY"){
            steps{
                sh "trivy image rameshkumarverma/spock-lizard-docker:latest > trivyimage.txt"
            }
        }
        stage("deploy_docker"){
            steps{
                sh "docker run -d --name spock-lizard-docker -p 8080:8080 rameshkumarverma/spock-lizard-docker:latest"
            }
        }

        // stage('Deploy to kubernets'){
        //     steps{
        //         script{
        //             // dir('K8S') {
        //                 withKubeConfig(caCertificate: '', clusterName: '', contextName: '', credentialsId: 'k8s', namespace: '', restrictKubeConfigAccess: false, serverUrl: '') {
        //                         sh 'kubectl apply -f deployment-service.yml'
        //                         // sh 'kubectl apply -f service.yml'
        //                 }
        //             // }
        //         }
        //     }
        // }

    }
}
