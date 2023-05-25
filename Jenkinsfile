pipeline {
    agent any
   
    stages {
        stage('Clone Repo') {
            steps {
                echo 'Not required as git clone is handled by Jenkins directly'
                //git url: 'https://github.com/timchristopher/duo-task', branch: 'master'
            }
        }
        stage('Pre-build Cleanup') {
            steps {
                echo 'Clean-up'
                // sh 'docker system prune -f'
                sh 'docker stop mysql && docker rm mysql || docker rm mysql'
                sh 'docker stop flask-app && docker rm flask-app || docker rm flask-app'
                sh 'docker stop trio-nginx'
            }
        }
        stage('Build and Run') {
            steps {
                echo 'Build'
                sh '''
                docker network inspect trio-net && sleep 1 || docker network create trio-net
                
                cd db
                docker build -t trio-db:v1 .
                docker run -d --network trio-net --name mysql trio-db:v1
                cd ..
                
                cd flask-app
                docker build -t trio-app:v1 .
                docker run -d --network trio-net --name flask-app trio-app:v1
                cd ..
                
                cd nginx
                docker run -d --network trio-net --name trio-nginx -p 80:80 --mount type=bind,source=$(pwd)/nginx.conf,target=/etc/nginx/nginx.conf  nginx:alpine
                cd ..
                '''
            }
        }
        stage('Test') {
            steps {
                echo 'Test'
            }
        }
        stage('Remote stage') {
            steps {
                echo 'Remote test'
            }
        }
    }

}
