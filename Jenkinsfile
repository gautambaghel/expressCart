pipeline {
  
    agent {
      kubernetes {
        label 'maven-app'
        defaultContainer 'jnlp'
        yaml """
            apiVersion: v1
            kind: Pod
            spec:
              containers:
              - name: maven
                image: maven:3.3.9-jdk-8-alpine
                command:
                - cat
                tty: true
            """
      }
    }

    environment {
        //put your own environment variables
        REGISTRY_URI = "docker.io/gautambaghel"
        BLACKDUCK_ACCESS_TOKEN  = credentials('jenkins-blackduck-access-token')
        POLARIS_ACCESS_TOKEN = credentials('jenkins-polaris-access-token')
    }
 
    stages {

        stage('Initial Notification') {
            steps {
                 //put webhook for your notification channel 
                 sh 'ls'
                 echo 'Pipeline Start Notification'
            }
        }

        stage('Static Code Analysis') {           
            steps {
              //put your code scanner 
              container('maven') {
                echo 'Code Scanning and Analysis'
                sh 'mvn --version'
              }
            }
        }
  
        stage('Automated Testing') {
            container('maven') {
              steps {
                  //put your Testing
                  echo 'Robot Testing Start'
                  sh 'mvn test'
              }
            }
            post{
                success{
                    echo "Automated Testing Successfully"
                }
                failure{
                    echo "Automated Testing Failed"
                }
            }
        }

        stage('Open Source Composition Scan') {
              container('maven') {
                steps {
                    //Put your image scanning tool 
                    echo 'Image Scanning Start'
                    sh 'wget https://detect.synopsys.com/detect.sh'
                    sh 'chmod +x detect.sh'
                    sh './detect.sh \
                        --blackduck.url="https://bizdevhub.blackducksoftware.com" \
                        --blackduck.api.token="${BLACKDUCK_ACCESS_TOKEN}" \
                        --blackduck.trust.cert=true \
                        --detect.project.name="CloudBeesDucky" \
                        --detect.tools="DETECTOR" \
                        --detect.project.version.name="DETECTOR_${BUILD_TAG}"'
                }
                post{
                    success{
                        echo "Composition Scanning Successfully"
                    }
                    failure{
                        echo "Composition Scanning Failed"
                    }
                }
              }
            }

        stage("Deploy to Staging"){
              when {
                  branch 'staging'
              }
              steps {
                  kubernetesDeploy kubeconfigId: 'kubeconfig-credentials-id', configs: 'build-pod.yaml', enableConfigSubstitution: true  // REPLACE kubeconfigId
              }
              post{
                  success{
                      echo "Successfully deployed to Staging"
                  }
                  failure{
                      echo "Failed deploying to Staging"
                  }
              }
          }

        stage("Deploy to Production"){
                when {
                    branch 'cloudbees-ci'
                }
                steps { 
                    // kubernetesDeploy kubeconfigId: 'kubeconfig-credentials-id', configs: 'build-pod.yaml', enableConfigSubstitution: true  // REPLACE kubeconfigId
                    docker ps
                }
                post{
                    success{
                        echo "Successfully deployed to Production"
                    }
                    failure{
                        echo "Failed deploying to Production"
                    }
                }
            }

      }
        
    post{
        always{
    step([
             //put your Testing
            ])
        }
        success{
            //notification webhook
            echo 'Pipeline Execution Successfully Notification'
        }
        failure{
            //notification webhook
            echo 'Pipeline Execution Failed Notification'
        }
    }
}