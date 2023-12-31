pipeline {
  agent any

  tools {
    maven 'M2_HOME'
    
    }
  environment {
        AWS_ACCESS_KEY_ID     = credentials('AWS_ACCESS_KEY_ID')
        AWS_SECRET_ACCESS_KEY = credentials('AWS_SECRET_ACCESS_KEY') 
  } 
  
  stages {
   stage('CheckOut') {
      steps {
        echo 'Checkout the source code from GitHub'
        git branch: 'main', url: 'https://github.com/swethaenukonda/Healthcare.git'
            }
    }
    
    stage('Package the Application') {
      steps {
        echo " Packaging the Application"
        sh 'mvn clean package'
            }
    }
    
    stage('Publish Reports using HTML') {
      steps {
      publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: '/var/lib/jenkins/workspace/Healthcare/target/surefire-reports', reportFiles: 'index.html', reportName: 'HTML Report', reportTitles: '', useWrapperFileDirectly: true])
            }
    }
    
   stage('Docker Image Creation') {
      steps {
        sh 'docker build -t swethamba859/healthcare-project:3.0 .'
            }
    }
    stage('DockerLogin') {
      steps {
          withCredentials([usernamePassword(credentialsId: 'docker1hub', passwordVariable: 'docker_password', usernameVariable: 'docker_user')]) {
         sh 'docker login -u ${docker_user} -p ${docker_password}'
            }
        }
    } 
  
    stage('Push Image to DockerHub') {
      steps {
        sh 'docker push swethamba859/healthcare-project:3.0'
            }
    } 
        stage ('Configure Test-server with Terraform, Ansible and then Deploying'){
            steps {
                dir('my-serverfiles'){
                sh 'sudo chmod 600 jenkinskey.pem'
                sh 'terraform init'
                sh 'terraform validate'
                sh 'terraform apply --auto-approve'
                }
            }
        }
     stage('deploy the application to kubernetes'){
steps{
  sh 'sudo chmod 600 ./jenkinskey.pem'    
  sh 'sudo scp -o StrictHostKeyChecking=no -i ./jenkinskey.pem deploymentservice.yml ubuntu@65.2.184.9:/home/ubuntu/'
  
script{
  try{
  sh 'ssh -o StrictHostKeyChecking=no -i ./jenkinskey.pem ubuntu@65.2.184.9 kubectl apply -f .'
  }catch(error)
  {
  sh 'ssh -o StrictHostKeyChecking=no -i ./jenkinskey.pem ubuntu@65.2.184.9 kubectl apply -f .'
  }
}
}
}
       /* stage ('Deploy into test-server using Ansible') {
           steps {
             ansiblePlaybook credentialsId: 'jenkinskey', disableHostKeyChecking: true, installation: 'ansible', inventory: 'inventory', playbook: 'healthcare-playbook.yml'
           }
               }
  
        stage('Deploying App to Kubernetes') {
      steps {
        script {
          kubernetesDeploy(configs: "deploymentservice.yml", kubeconfigId: "kubernetes")
    }
  }
}*/
      }
        }

  




          
