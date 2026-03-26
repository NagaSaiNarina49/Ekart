pipeline {
    agent {label 'naga-jenkins-agent'}
    
    tools
    {
        maven "maven3"
        jdk "jdk17"
        
    }
    environment{
        SCANNER_HOME=  tool 'sonar-scanner'
    }
   
    stages {
        stage('checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/NagaSaiNarina49/Ekart.git'
            }
        }
        
         stage('compile') {
            steps {
                sh "mvn compile"
            }
        }
        
         stage('unit tests') {
            steps {
                sh "mvn test -DskipTests=true"
            }
        }
        
         stage('sonarqube') {
            steps {
            withSonarQubeEnv('sonar') {
                sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectKey=EKART  -Dsonar.projectName=EKART\
                -Dsonar.java.binaries=. '''
                
              }
            }
        }
        
         stage('build') {
            steps {
                sh "mvn package -DskipTests=true"
            }
        }
        stage('deploy to nexus') {
            steps {
               withMaven(globalMavenSettingsConfig: 'global-maven', jdk: 'jdk17', maven: 'maven3', traceability: true) {
                   sh " mvn deploy -DskipTests=true"
                   } 
            }
        }
        
          stage('Docker image build and tag') 
          {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker')
                    {
                    sh " docker build -t nagasai49/ekart:${BUILD_NUMBER} -f docker/Dockerfile ."
                    }
                     }
                }
      }
      
        stage('Docker push') 
          {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker')
                    {
                    sh " docker push nagasai49/ekart:${BUILD_NUMBER} "
                    }
                     }
                }
      }
            
        }
        
        
    }


