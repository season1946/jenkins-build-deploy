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
          - name: docker
            image: docker:latest
            command:
            - cat
            tty: true
            volumeMounts:
             - mountPath: /var/run/docker.sock
               name: docker-sock
          volumes:
          - name: docker-sock
            hostPath:
              path: /var/run/docker.sock  
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
    stage('Build-project') {
      steps {
        container('kubectl') {
          echo 'Empty'
        }
      }
    }
    stage('Build-Docker-Image') {
      steps {
        container('docker') {
          sh 'docker build -t 349361870252.dkr.ecr.us-west-2.amazonaws.com/jenkins-demo:latest .'
        }
      }
    }
    stage('Login-push') {
      steps {
        container('docker') {
          sh 'docker login -u victorgucanada -p Jing723211'
          sh 'docker push 349361870252.dkr.ecr.us-west-2.amazonaws.com/jenkins-demo:latest'
      }
     }
    }
    stage('deploy-Image') {
      steps {
        container('kubectl') {
             sh 'kubectl apply -f deployment.yml'
             sh 'kubectl rollout restart deployment demo-app-deploy'
        }
      }
    }


  }

}