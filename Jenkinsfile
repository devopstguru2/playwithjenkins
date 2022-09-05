pipeline {

  environment {
    registry = "ishwary/devops"
    registryCredential = 'dockerhub'
    dockerImage = ""
    
  }

  agent any

  stages {

    stage('Checkout Source') {
      steps {
        git 'https://github.com/justmeandopensource/playjenkins.git'
      }
    }

    stage('Build image') {
      steps{
        script {
          dockerImage = docker.build registry + ":$BUILD_NUMBER"
        }
      }
    }

    stage('Push Image') {
      steps{
        script {
          docker.withRegistry( "", registryCredential) {
            dockerImage.push()
          }
        }
      }
    }

    stage('Deploy to GKE') {
      steps {
         sh "sed -i 's/hello:latest/hello:${env.BUILD_ID}/g' myweb.yaml"
         
        step([$class: 'KubernetesEngineBuilder', projectId: env.PROJECT_ID, clusterName: env.CLUSTER, location: env.LOCATION, manifestPattern: 'deployment.yaml', credentialsId: env.CREDENTIALS_ID, verifyDeployments: true])
      }
    }

  }

}
