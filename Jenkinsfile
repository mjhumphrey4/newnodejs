pipeline {
        agent any
        }
    stages {
        stage("Checkout code") {
            steps {
                checkout scm
            }
        }
        stage("Build image") {
            steps {
                script {
                    myapp = docker.build("sjc.vultrcr.com/example/node-app:${env.BUILD_ID}")
                }
            }
        }
        stage("Push image") {
            steps {
                script {
                    docker.withRegistry('https://sjc.vultrcr.com/example/', 'vcr-credentials') {
                            myapp.push("latest")
                            myapp.push("${env.BUILD_ID}")
                    }
                }
            }
        }
      stage("Deploy to Vultr Kubernetes") {
         steps {
            sh "sed -i 's/node-app:latest/node-app:${env.BUILD_ID}/g' deployment.yml"
                script {
       withKubeConfig([credentialsId: 'kubeconfig']) 
        {
          sh "kubectl apply -f deployment.yml"
          sh "kubectl apply -f service.yml"

     }                
    }
   }
  }    
 }
