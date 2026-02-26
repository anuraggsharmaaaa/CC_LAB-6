pipeline {
    agent any
    stages {
        stage('Build Backend Image') {
            steps {
                sh 'docker build -t backend-app backend'
            }
        }
        stage('Deploy Backend Containers') {
            steps {
                sh '''
                docker network create mynet || true
                docker rm -f backend1 backend2 || true
                docker run -d --name backend1 --network mynet backend-app
                docker run -d --name backend2 --network mynet backend-app
                '''
            }
        }
        stage('Deploy NGINX Load Balancer') {
            steps {
                sh '''
                docker rm -f nginx-lb || true
                docker run -d --name nginx-lb --network mynet -p 80:80 nginx
                docker cp nginx/default.conf nginx-lb:/etc/nginx/conf.d/default.conf
                docker exec nginx-lb nginx -s reload
                '''
            }
        }
        stage('Post Actions') {
            steps {
                echo 'Pipeline executed successfully, NGINX load balancer is running.'
            }
        }
    }
}
