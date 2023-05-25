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

                sh '''#!/bin/bash
                declare -a arr=("mysql" "flask-app" "trio-nginx")
                for i in "${arr[@]}"
                do
                    echo "Clean-up: $i"
                    docker ps -q --filter "name=^$i\$" | grep -q . && docker stop $i | (echo -n "Stopped " && cat) || echo "$i not running"
                    docker ps -qa --filter "name=^$i\$" | grep -q . && docker rm $i | (echo -n "Removed container " && cat) || echo "Container named '$i' does not exist"
                    echo
                done
                unset arr
                '''
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
