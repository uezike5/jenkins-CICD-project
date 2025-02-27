pipeline {
  agent any

  tools {
  maven 'Maven3'
  }

  stages {

    stage('Code Checkout from github Repo') {
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

stage ('JaCoCo') {
      steps {
      jacoco()
      }
    }

    stage ('Manager Approval Required.') {
      steps {
      echo "Taking approval from Manager before QA Deployment"
        timeout(time: 1, unit: 'DAYS') {
        input message: 'Do you want to deploy this application?', submitter: 'admin'
        }
      }
    }
    
    stage ('Nexus Upload') {
      steps {
      nexusArtifactUploader(
      nexusVersion: 'nexus3',
      protocol: 'http',
      nexusUrl: '100.26.145.254:8081',
      groupId: 'JavaWebCalculator-master',
      version: '1.0-SNAPSHOT',
      repository: 'maven-snapshots',
      credentialsId: 'nexus',
      artifacts: [
      [artifactId: 'JavaWebCalculator-master',
      classifier: '',
      file: 'JavaWebCalculator-master/target/webapp-0.3.war',
      type: 'war']
      ])
      }
    }


    stage ('QA Deployment') {
      steps {
        echo "deploying to QA Env "
deploy adapters: [tomcat9(credentialsId: 'tomcat', path: '', url: 'http://54.162.0.156:8080')], contextPath: null, war: '**/*.war'        }
    }
}


}

