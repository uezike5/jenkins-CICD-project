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
    
    stage ('Manager Approval Required.') {
      steps {
      echo "Taking approval from DEV Manager for QA Deployment"
        timeout(time: 1, unit: 'DAYS') {
        input message: 'Do you want to deploy this application?', submitter: 'admin'
        }
      }
    }
    
    stage ('QA Deployment') {
      steps {
        echo "deploying to QA Env "
deploy adapters: [tomcat9(credentialsId: 'tomcat', path: '', url: 'http://54.234.93.243:8080')], contextPath: null, war: '**/*.war'        }
    }
}
  
  
}

