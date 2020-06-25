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
          sh 'npm install'
        }

      }
    }
    stage ('Deploy to Testing Environment') {
      steps {
        container ('nodejs') {
          echo "deploy nodejs module to testing environment"
        }
      }
    }

           
    stage('Scan') {
      steps {
        container ('java') {
          synopsys_detect(detectProperties: '--blackduck.url="https://bizdevhub.blackducksoftware.com"  --blackduck.api.token="${BLACKDUCK_ACCESS_TOKEN}"  --detect.tools="SIGNATURE_SCAN"', returnStatus: true)
        }
      }
    }

  }

  environment {
    BLACKDUCK_ACCESS_TOKEN = credentials('jenkins-blackduck-access-token')
  }
 } 