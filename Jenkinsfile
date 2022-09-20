pipeline {
    agent any
    options {
        skipStagesAfterUnstable()
    }
    stages {
         stage('Clone repository') { 
            steps { 
                script{
                checkout scm
                }
            }
        }
        stage('Deploy'){
         container('kubectl') {
            steps {
                 sh 'kubectl apply -f deployment.yml'
                 sh 'kubectl rollout restart deployment demo-app-deploy'
            }
         }
        }

    }
}
