pipeline {
 agent {
    kubernetes {
      label 'nodejs-app'
      yamlFile 'build-pod.yaml'
    }
  }
 
  stages {
    stage('Build') {
      steps {
        container ('nodejs') {
          sh 'mvn clean package'
        }

      }
    }
    stage ('Deploy to Testing Environment') {
      steps {
        container ('nodejs') {
          echo "deploy target/*.war to testing environment"
        }
      }
    }

           
    stage('Scan') {
      steps {
        container ('nodejs') {
          synopsys_detect(detectProperties: '--blackduck.url="https://bizdevhub.blackducksoftware.com"  --blackduck.api.token="${BLACKDUCK_ACCESS_TOKEN}"  --detect.tools="DETECTOR"', returnStatus: true)
        }
      }
    }

  }

  environment {
    BLACKDUCK_ACCESS_TOKEN = credentials('jenkins-blackduck-access-token')
  }
 } 