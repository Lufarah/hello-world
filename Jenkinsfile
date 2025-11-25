pipeline {
    agent any

    stages {

        stage('Build') {
            steps {
                echo 'Instalando dependencias...'
                sh 'pip install -r requirements.txt || true'
            }
        }

        stage('Test') {
            steps {
                echo 'No hay pruebas unitarias para ejecutar.'
            }
        }

        stage('Deploy') {
            steps {
                echo 'Desplegando aplicación en entorno de pruebas...'
            }
        }
    }

    post {
        success { echo 'Pipeline ejecutada exitosamente.' }
        failure { echo 'La pipeline falló.' }
    }
}
