pipeline {
    agent any

    environment {
        COMPOSE_PROJECT_NAME = "wordpress_docker_jenkins"
    }

    stages {

        stage('Checkout') {
            steps {
                echo 'Descargando el codigo desde el repositorio Git...'
                checkout scm
            }
        }

        stage('Detener contenedores previos') {
            steps {
                echo 'Deteniendo y eliminando contenedores anteriores (si existen)...'
                sh 'docker compose down'
            }
        }

        stage('Levantar ambiente') {
            steps {
                echo 'Construyendo y levantando los contenedores con Docker Compose...'
                sh 'docker compose up -d'
            }
        }

        stage('Verificar despliegue') {
            steps {
                echo 'Verificando que los contenedores esten corriendo...'
                sh 'docker ps'
                sh 'docker network ls'
                sh 'docker volume ls'
            }
        }
    }

    post {
        success {
            echo 'Despliegue completado exitosamente. WordPress disponible en http://localhost:8081'
        }
        failure {
            echo 'El pipeline fallo. Revisar los logs para mas detalles.'
        }
        always {
            echo 'Pipeline finalizado.'
        }
    }
}
