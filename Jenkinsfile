pipeline {
    agent any
    
      
    stages {
        stage('checkout source code') {
            steps {
                git url: "https://github.com/gitnicholas01/lbg-trio-task.git", branch: "master"
            }
        }
        
        stage('Build') {
            steps {
                sh '''docker build -t trio-app:v1 .
		cd ../db
                docker build -t trio-db:v1 .
                   '''
            }
        }
        stage('run containers') {
            steps {
                sh '''
                docker network inspect trio-net && sleep 1 || docker network create trio-net
                docker run -d -p --network trio-net --name mysql trio-db:v1
		docker run -d -p --network trio-net --name flask-app trio-app:v1
                docker run -d -p 80:80 --network trio-net --mount type=bind,source=$(pwd)/nginx/nginx.conf,target=/etc/nginx/nginx.conf
		
                '''
            }
        }
    }
}
