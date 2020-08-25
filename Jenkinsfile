pipeline {
  
    agent none
    environment {
        //put your own environment variables
        REGISTRY_URI = "docker.io/gautambaghel"
    }
 
    stages {

        stage('Initial Notification') {
            steps {
                 //put webhook for your notification channel 
                 echo 'Pipeline Start Notification'
            }
        }

        stage('Code Analysis') {           
            steps {
              //put your code scanner 
                echo 'Code Scanning and Analysis'
            }
        }
  
        stage('Robot Testing') {
            steps {
                //put your Testing
                echo 'Robot Testing Start'
            }
            post{
                success{
                    echo "Robot Testing Successfully"
                }
                failure{
                    echo "Robot Testing Failed"
                }
            }
        }

        stage('Image Scan') {
                steps {
                    //Put your image scanning tool 
                    echo 'Image Scanning Start'
                }
                post{
                    success{
                        echo "Image Scanning Successfully"
                    }
                    failure{
                        echo "Image Scanning Failed"
                    }
                }
            }

        stage("Deploy to Production"){
                when {
                    branch 'cloudbees-ci'
                }
                steps { 
                    kubernetesDeploy kubeconfigId: 'kubeconfig-credentials-id', configs: 'build-pod.yaml', enableConfigSubstitution: true  // REPLACE kubeconfigId
    
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