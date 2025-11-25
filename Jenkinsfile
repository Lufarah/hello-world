pipeline {
    agent {
        docker {
            image 'python:3.11'
            args '-u'
        }
    }

    stages {

        stage('Build') {
            steps {
                echo 'Instalando dependencias...'
                sh '''
                    if [ -f requirements.txt ]; then
                        pip install -r requirements.txt
                    fi
                '''
            }
        }

        stage('Test') {
            steps {
                echo 'Ejecutando pruebas unitarias con pytest...'
                sh '''
                    pip install pytest pytest-cov
                    pytest
                '''
            }
        }

        stage('Deploy') {
            steps {
                echo 'Desplegando aplicación en entorno de pruebas...'
                // Ejemplo:
                // sh 'docker build -t mi-app:test .'
                // sh 'docker run -d mi-app:test'
            }
        }
    }

    post {
        success { echo 'Pipeline completada exitosamente.' }
        failure { echo 'La pipeline falló.' }
    }
}
