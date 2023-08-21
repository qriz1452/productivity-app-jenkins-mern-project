pipeline {
    agent any
    
    enviroment {
        MONGODB_URI = credentials('mongodb-uri')
        TOKEN_KEY = credentials('token-key')
        EMAIL = credentials('email')
        PASSWOD =  credentials('password')
    }
    
    stages {
        stage('git checkout') {
            // This stage is responsible for fetching the latest version of the source code
            // from the Git repository configured in the Jenkins job settings.
            // It ensures that the subsequent stages operate on the most up-to-date codebase.
            
            steps {
                checkout scm
                
            }
        }
        stage('Client Tests') {
            // Frontend code is in client drecory
            // It navigates to the 'client' directory, installs npm dependencies, and then runs tests using npm.
            
            steps {
                dir('client') {
                    sh 'npm install'
                    sh 'npm test'
                }
            }
        }
        stage('Server tests') {
            // Server directory have backend code
            // Change the current directory to 'server' for the following steps.
            // npm install : Install necessary dependencies for the server.
            // Set environment variables for the tests. and running test using npm test
            
            steps {
                dir('server'){
                    sh 'npm install'
                    sh 'export MONGODB_URI=$MONGODB_URI'
                    sh 'export TOKEN_KEY=$TOKEN_KEY'
                    sh 'export EMAIL=$EMAIL'
                    sh 'export PASSWORD=$PASSWORD'
                    sh 'npm test'
                }
            }
        }
        stage('Build Images') {
            // This stage is responsible for building Docker images for the client and server components of the MERN project.
            // It uses Docker commands to build images and tag them with appropriate names and versions and used dockerfilr in their respective client and server directory .

            
            steps {
                sh 'docker build -t qriz1452/productivity-app-jenkins-mern-project:client client'
                sh 'docker build -t qriz1452/productivity-app-jenkins-mern-project:server server'
                
            }
        }
        stage('Push image to dockerhub') {
            // This stage pushes Docker images to Docker Hub after a successful build.
            // Authenticate with Docker Hub using the provided credentials.
            // The 'dockerhub' credential ID is used to fetch the Docker Hub username and password.
            // Log in to Docker Hub using the fetched credentials
            // The client and server images are tagged as 'client-latest' and 'server-latest'.
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub', passwordVariable: 'DOCKER_PASSWORD', usernameVariable: 'DOCKER_USERNAME')]) {
                    sh 'docker login -u $DOCKER_USERNAME -p $DOCKER_PASSWORD'
		        	sh 'docker push -t qriz1452/productivity-app-jenkins-mern-project:client-latest'
		        	sh 'docker push -t qriz1452/productivity-app-jenkins-mern-project:server-latest'
                }
                
            }
        }
        
    }
}
