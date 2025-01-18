pipeline {
    agent any
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('Security Testing with OWASP ZAP') {
            steps {
                script {
                    // Exécuter ZAP dans le pipeline
                    zapCli = tool name: 'OWASP ZAP', type: 'ToolLocation'
                    zapDockerImage = 'owasp/zap2docker-stable'

                    // Lancer ZAP en mode daemon (en arrière-plan)
                    sh "docker run -d -p 8091:8080 --name zap ${zapDockerImage} zap.sh -daemon -port 8080"

                    // Attendre un peu pour que ZAP démarre
                    sleep(10)

                    // Lancer l'analyse avec ZAP (vous pouvez ajuster les paramètres selon vos besoins)
                    sh "docker exec zap zap-cli quick-scan -r zap_report.html http://localhost:8080"
                }
            }
        }
    }
    post {
        always {
            // Nettoyer les containers ZAP après chaque exécution
            sh "docker stop zap && docker rm zap"
        }
    }
}
