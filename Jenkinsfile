pipeline {
    agent any
    stages {
        stage('Start ZAP') {
            steps {
                script {
                    // Démarrer ZAP dans un conteneur Docker
                    sh 'docker run -d -p 8091:8080 --name zap owasp/zap2docker-stable zap.sh -daemon -port 8080'
                }
            }
        }
    }
}
