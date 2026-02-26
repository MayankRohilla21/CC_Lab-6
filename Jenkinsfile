pipeline {
    agent any

    stages {

        stage('Create Network') {
            steps {
                sh '''
                docker network inspect lab6-network >/dev/null 2>&1 || docker network create lab6-network
                '''
            }
        }

        stage('Build Backend Image') {
            steps {
                sh 'docker build -t backend-app backend'
            }
        }

        stage('Deploy Backends') {
            steps {
                sh '''
                docker rm -f backend1 backend2 || true

                docker run -d --name backend1 --network lab6-network backend-app
                docker run -d --name backend2 --network lab6-network backend-app

                sleep 5
                '''
            }
        }

        stage('Start NGINX') {
            steps {
                sh '''
                docker rm -f nginx-lb || true

                docker run -d \
                  --name nginx-lb \
                  --network lab6-network \
                  -p 80:80 \
                  -v $(pwd)/nginx/default.conf:/etc/nginx/conf.d/default.conf \
                  nginx

                sleep 3
                '''
            }
        }

        stage('Verify Containers') {
            steps {
                sh 'docker ps'
            }
        }
    }

    post {
        success {
            echo 'Lab 6 completed successfully!'
        }
        failure {
            echo 'Pipeline failed. Check console logs.'
        }
    }
}
