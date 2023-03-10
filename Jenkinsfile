pipeline {
    
    agent any
    
    tools {
        maven 'Maven3'
    }
    stages {
        
        stage('Code Checkoutcfrom github Repo') {
            steps {
            checkout scmGit(branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[credentialsId: 'github', url: 'https://github.com/uezike5/jenkins-CICD-project']]) 
            }      
        }
        
        stage ('Build with Maven3') {
            steps {
                  sh 'mvn clean install -f JavaWebCalculator-master/pom.xml'
            }  
        }
        
        stage ('Code Quality Scanning') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    sh 'mvn -f JavaWebCalculator-master/pom.xml sonar:sonar'
                    }
            }
        }

        stage ('Managers approval required prior Production Deployment') {
            steps {
                echo "Taking approval from QA manager" timeout(time: 1, unit: 'DAYS') {
                    input message: 'Do you want to deploy to Production?', submitter: 'admin,manager_userid'
                    }
            }
        }

        stage ('Deploy to Tomcat') {
            steps {
                echo "deploying to DEV Env " deploy adapters: [tomcat9(credentialsId: 'tomcat', path: '', url: 'http://54.234.93.243:8080')], contextPath: null, war: '**/*.war'    }
        }
    }
}
