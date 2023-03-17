pipeline {
  agent {label 'batman'}
  options {
    buildDiscarder(logRotator(numToKeepStr: '5'))
  }
  environment {
    DOCKERHUB_CREDENTIALS = credentials('dockerhub_access_token')
  }
  stages {
    stage('Build') {
      steps {
        sh 'docker build -t anirban9/react-k8s-app .'
      }
    }
    stage('Login') {
      steps {
        sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
      }
    }
    stage('Push') {
      steps {
        sh 'docker push anirban9/react-k8s-app'
      }
    }
    stage('Deploy to K8s GKE') {
      agent {label 'k8s-agent'}
      script {
          kubernetesDeploy(configs: "./k8s/*.yaml", kubeconfigId: "gke-cluster101")
        }
    }
  }
  post {
    always {
      sh 'docker logout'
    }
  }
}
