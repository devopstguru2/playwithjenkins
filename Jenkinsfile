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
         
        step([$class: 'KubernetesEngineBuilder', projectId: "nomadic-device-361610", clusterName: 'autopilot-cluster-1', region: 'us-central1', manifestPattern: 'myweb.yaml', credentialsId: gke, verifyDeployments: true])
      }
    }

  }

}
