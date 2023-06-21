pipeline {
    agent any
    
    environment {
        DB_PASSWORD = credentials('DB_PASSWORD')
    }
    
    stages {
        stage('checkout source code') {
            steps {
                git url: "https://github.com/gitnicholas01/lbg-trio-task", branch: "master"
            }
        }
        stage('cleanup') {
            steps {
                sh '''
                if docker logs chaperoo-db; then
                    if docker exec chaperoo-db ls; then
                        docker stop chaperoo-db
		            else
			            sleep 1
                    fi
                    docker rm chaperoo-db
		        else
		            sleep 1
                fi
                if docker logs chaperoo-service; then
                    if docker exec chaperoo-service ls; then
                        docker stop chaperoo-service
		            else
			            sleep 1
                    fi
                    docker rm chaperoo-service
		        else
		            sleep 1
                fi
                if docker logs chaperoo-client; then
                    if docker exec chaperoo-client ls; then
                        docker stop chaperoo-client
		            else
			            sleep 1
                    fi
                    docker rm chaperoo-client
		        else
		            sleep 1
                fi
                docker rmi chaperoo-client:v1
                '''
            }
        }
        stage('Build') {
            steps {
                sh 'docker build -t chaperoo-client:v2 .'
            }
        }
        stage('run containers') {
            steps {
                sh '''
                docker network inspect chaperoo-net && sleep 1 || docker network create chaperoo-net
                docker run -d -e MYSQL_ROOT_PASSWORD=${DB_PASSWORD} --name chaperoo-db --network chaperoo-net jordangrindrod/chaperoo-db:latest
                docker run -d -e DB_PASSWORD=${DB_PASSWORD} --name chaperoo-service --network chaperoo-net jordangrindrod/chaperoo-service:latest
                docker run -d -p 80:80 --network chaperoo-net --name chaperoo-client chaperoo-client:v2
                '''
            }
        }
    }
}
