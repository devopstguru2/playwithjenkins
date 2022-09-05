pipeline {

  environment {
    registry = "ishwary/devops"
    registryCredential = 'dockerhub'
    dockerImage = ""
    export PROJECT='nomadic-device-361610'
    export CLUSTER='autopilot-cluster-1'
    export REGION='us-central1'
    export SA="my-service-account"
    export SA_EMAIL='${SA}@${PROJECT}.iam.gserviceaccount.com'
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
