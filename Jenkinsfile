pipeline {
    agent any

    stages {

        stage('Build') {
            steps {
                echo 'Instalando dependencias en contenedor Docker...'
                sh '''
                    docker run --rm -v "$PWD":/app -w /app python:3.11 \
                        pip install -r requirements.txt || true
                '''
            }
        }

        stage('Test') {
            steps {
                echo 'Ejecutando tests dentro de un contenedor Docker...'
                sh '''
                    docker run --rm -v "$PWD":/app -w /app python:3.11 \
                        sh -c "pip install pytest pytest-cov && pytest"
                '''
            }
        }

        stage('Deploy') {
            steps {
                echo 'Fase de despliegue (puedes agregar tus comandos)...'
            }
        }
    }

    post {
        success { echo "Pipeline completada exitosamente." }
        failure { echo "La pipeline falló." }
    }
}
