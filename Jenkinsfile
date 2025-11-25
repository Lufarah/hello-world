pipeline {
    agent any

    environment {
        // SonarQube (DEBE coincidir con el nombre del servidor configurado en Jenkins)
        SONARQUBE_ENV = 'MySonarQube'

        // Directorio donde Dependency-Check generará reportes
        DEP_CHECK_REPORT = "dependency-check-report.xml"
    }

    stages {

        stage('Build') {
            steps {
                echo "Instalando dependencias para la aplicación Flask..."
                sh '''
                    docker run --rm -v "$PWD":/app -w /app python:3.11 \
                    pip install -r requirements.txt
                '''
            }
        }

        stage('Test') {
            steps {
                echo "Ejecutando pruebas unitarias con pytest..."
                sh '''
                    docker run --rm -v "$PWD":/app -w /app python:3.11 \
                    sh -c "pip install pytest && pytest"
                '''
            }
            post {
                always {
                    junit 'tests/*.xml'
                }
            }
        }

        stage('Analyze - SonarQube') {
            steps {
                echo "Ejecutando análisis de calidad con SonarQube..."
                withSonarQubeEnv("${SONARQUBE_ENV}") {
                    sh """
                        sonar-scanner \
                        -Dsonar.projectKey=hello-world \
                        -Dsonar.sources=. \
                        -Dsonar.python.version=3.11 \
                        -Dsonar.host.url=\$SONAR_HOST_URL \
                        -Dsonar.login=\$SONAR_AUTH_TOKEN
                    """
                }
            }
        }

        stage('Security Test - OWASP Dependency-Check') {
            steps {
                echo "Ejecutando OWASP Dependency-Check..."
                dependencyCheck additionalArguments: "--scan . --out . --format XML",
                                odcInstallation: "Default"
            }
            post {
                always {
                    dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
                }
            }
        }

        stage('Security Test - OWASP ZAP') {
            steps {
                echo "Iniciando OWASP ZAP..."
                sh '''
                    docker run -u root -d --name zap -p 8090:8080 \
                        ghcr.io/zaproxy/zaproxy:stable zap.sh -daemon

                    echo "Esperando que ZAP termine de iniciar..."
                    sleep 25

                    docker exec zap zap-baseline.py \
                        -t http://host.docker.internal:5000 \
                        -r zap_report.html

                    docker cp zap:/zap/zap_report.html .
                    docker stop zap
                    docker rm zap
                '''
            }
            post {
                always {
                    archiveArtifacts artifacts: 'zap_report.html', allowEmptyArchive: true
                }
            }
        }

        stage('Deploy') {
            steps {
                echo "Desplegando aplicación Flask en entorno de prueba..."
                sh '''
                    docker stop flask-test || true
                    docker rm flask-test || true
                    
                    docker run -d --name flask-test \
                        -p 5000:5000 \
                        -v "$PWD":/app \
                        -w /app python:3.11 \
                        python app.py
                '''
            }
        }
    }

    post {
        failure {
            echo "La pipeline falló."
        }
        success {
            echo "Pipeline completada exitosamente."
        }
    }
}
