pipeline{
    
    agent any 
    
    stages {
        
        stage('Git Checkout'){
            
            steps{
                
                script{
                    
                    git branch: 'main', credentialsId: 'git-credentials', url: 'https://github.com/vallakatii/demo-counter-app.git'
                }
            }
        }
        stage('unit testing'){
            
            steps{
                
                script{
                    sh 'mvn test'
                }
            }
        }
        stage('integration testing'){
            
            steps{
                
                script{
                    sh 'mvn verify -DskipUnitTests'
                }
            }
        }
        stage('maven build'){
            
            steps{
                
                script{
                    sh 'mvn clean package'
                }
            }
        }
        stage('static code analysis '){
            
            steps{
                script{
                    withSonarQubeEnv(credentialsId: 'sonar-credentials') {
                        sh 'mvn clean package sonar:sonar'
                    }
                }
            }
        }
        stage('Qualitygatestatus'){
            
            steps{
                script{
                    waitForQualityGate abortPipeline: false, credentialsId: 'sonar-credentials'
                }
            }
                
            
            
        }    
            
        stage('uploading artifact to nexus'){
            
            steps{
                
                script{
                    
                    def readPomVersion = readMavenPom file: 'pom.xml'
                    
                    def nexusRepo = readPomVersion.version.endsWith("SNAPSHOT") ? "demoapp-snapshot" : "demoapp-release"
                    
                    nexusArtifactUploader artifacts: [[artifactId: 'springboot', classifier: '', file: 'target/Uber.jar', type: 'jar']], credentialsId: 'nexus-cred', groupId: 'com.example', nexusUrl: '13.233.245.152:8081', nexusVersion: 'nexus3', protocol: 'http', repository: nexusRepo, version: "${readPomVersion.version}"
                    
                }
            }
        }
        
        stage('Docker Image Build and push to ECR'){
            
            steps{
                
               script{
                   
                    sh '''
	                whoami
                    aws configure set aws_access_key_id $AWS_ACCESS_KEY
	                aws configure set aws_secret_access_key $AWS_SECRET_KEY
                    aws configure set default.region ap-south-1
      
                    aws ecr get-login-password --region ap-south-1 | docker login --username AWS --password-stdin 591133068176.dkr.ecr.ap-south-1.amazonaws.com.amazonaws.com
	                docker build -t 591133068176.dkr.ecr.ap-south-1.amazonaws.com/demo_application:SAMPLE-PROJECT-${BUILD_NUMBER} .
                    docker push 591133068176.dkr.ecr.ap-south-1.amazonaws.com/demo_application:SAMPLE-PROJECT-${BUILD_NUMBER}
	                '''
                    
               }    
                   
        
            }
        }
             
    }         
}       
        
         
               
    


