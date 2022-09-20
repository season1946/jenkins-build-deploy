pipeline {
  agent {
    kubernetes {
      yaml '''
        apiVersion: v1
        kind: Pod
        spec:
          containers:
          - name: kubectl
            image: gcr.io/cloud-builders/kubectl
            command:
            - cat
            tty: true
   
        '''
    }
  }
  stages {
    stage('Clone') {
      steps {
        container('kubectl') {
          git branch: 'main', changelog: false, poll: false, url: 'https://github.com/season1946/jenkins-build-deploy.git'
        }
      }
    }  

    stage('Build-Docker-Image') {
      steps {
        container('kubectl') {
             sh 'kubectl apply -f deployment.yml'
             sh 'kubectl rollout restart deployment demo-app-deploy'
        }
      }
    }


  }

}