pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout scm  // Vérifier le code source à partir du dépôt Git
            }
        }

        stage('Start ZAP') {
            steps {
                script {
                    // Démarrer OWASP ZAP dans un conteneur Docker
                    sh 'docker run -d -p 8091:8080 --name zap owasp/zap2docker-stable zap.sh -daemon -port 8080'
                    sleep(10)  // Attendre quelques secondes pour permettre à ZAP de démarrer
                }
            }
        }

        stage('Run ZAP Scan') {
            steps {
                script {
                    // Exécuter le scan OWASP ZAP sur l'application cible
                    sh "docker exec zap zap-cli quick-scan -r zap_report.html https://github.com/1farahkefi/spring-petclinic"
                }
            }
        }

        stage('Stop ZAP') {
            steps {
                script {
                    // Arrêter et supprimer le conteneur Docker ZAP
                    sh 'docker stop zap && docker rm zap'
                }
            }
        }

        stage('Start SonarQube') {
            steps {
                script {
                    // Démarrer SonarQube dans un conteneur Docker
                    sh 'docker run -d --name sonarqube -p 9000:9000 sonarqube'
                    sleep(20)  // Attendre que SonarQube démarre
                }
            }
        }

        stage('Run SonarQube Analysis') {
            steps {
                script {
                    // Lancer l'analyse de SonarQube avec Maven
                    sh 'mvn clean verify sonar:sonar -Dsonar.host.url=http://localhost:9000'
                }
            }
        }

        stage('Stop SonarQube') {
            steps {
                script {
                    // Arrêter et supprimer le conteneur Docker SonarQube
                    sh 'docker stop sonarqube && docker rm sonarqube'
                }
            }
        }

        stage('Publish Reports') {
            steps {
                // Publier les rapports de ZAP et de SonarQube
                archiveArtifacts artifacts: '**/zap_report.html', allowEmptyArchive: true
                archiveArtifacts artifacts: '**/target/sonar/report-task.txt', allowEmptyArchive: true
            }
        }
    }
}
