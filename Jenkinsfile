pipeline {
    agent any

    stages {

        stage('Build') {
            steps {
                echo 'Compilando el código fuente...'
                // Ejemplos según tu proyecto:
                // sh 'mvn clean package'        // Java + Maven
                // sh 'npm install'              // Node.js
                // sh 'python setup.py build'    // Python
            }
        }

        stage('Test') {
            steps {
                echo 'Ejecutando pruebas unitarias...'
                // Ejemplos:
                // sh 'mvn test'
                // sh 'npm test'
                // sh 'pytest'
            }
            post {
                always {
                    junit '**/test-results/*.xml' // Ajusta según tus reportes
                }
            }
        }

        stage('Deploy') {
            steps {
                echo 'Desplegando la aplicación en entorno de pruebas...'
                // Ejemplo genérico de despliegue:
                // sh 'scp target/app.jar user@test-server:/var/www/test/'
                // sh 'kubectl apply -f k8s/deployment.yaml'
                // sh './deploy_test_env.sh'
            }
        }
    }

    post {
        success {
            echo 'Pipeline ejecutada exitosamente.'
        }
        failure {
            echo 'La pipeline falló.'
        }
    }
}
