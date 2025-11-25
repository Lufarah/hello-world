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
                        -Dsonar.login=
