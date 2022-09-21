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
          - name: awscli
            image: amazon/aws-cli
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
  environment{
    docker_pwd = credentials('dockerhub-pwd')
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
    stage('Login-ECR') {
      steps {
        container('awscli') {
          echo 'Empty'

      }
     }
    }
    stage('Login-push') {
      steps {
        container('docker') {
          sh 'curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"'
          sh 'unzip awscliv2.zip'
          sh 'sudo ./aws/install'
          sh 'docker login --username AWS --password $(aws ecr get-login-password --region us-west-2) 349361870252.dkr.ecr.us-west-2.amazonaws.com'
          sh 'docker push 349361870252.dkr.ecr.us-west-2.amazonaws.com/jenkins-demo:latest'
      }
     }
    }
    stage('deploy-Image-local') {
      steps {
        container('kubectl') {
             sh 'kubectl apply -f deployment.yml'
             sh 'kubectl rollout restart deployment demo-app-deploy  -n default'
        }
      }
    }
    stage('deploy-EKS011-asProd') {
      steps {
        container('kubectl') {
           withKubeCredentials(kubectlCredentials: [[caCertificate: '', clusterName: 'arn:aws:eks:us-west-2:349361870252:cluster/aws011-preprod-test-eks', contextName: '', credentialsId: 'EKS011-admin-token', namespace: 'kube-system', serverUrl: 'https://FCE040A44E47B0273102E6579D78DB6A.sk1.us-west-2.eks.amazonaws.com']]) {
             sh 'kubectl apply -f deployment.yml'
             sh 'kubectl rollout restart deployment demo-app-deploy  -n default'
            }
        }
      }
    }

  }

}
