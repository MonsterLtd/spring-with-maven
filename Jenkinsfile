pipeline {

    agent {
        label 'my-new-agent'
    }

    tools {
        nodejs 'nodejs'
        dockerTool 'docker'
    }

    triggers {
        githubPush()
    }

    environment {
        DOCKERHUB_CREDENTIALS = credentials('my-dockerhub-creds')
        IMAGE_NAME = 'nadia/spring-with-maven'
    }

    stages {

        stage('Clean') {
            steps {
                cleanWs()
            }
        }

        stage('Clone_Repo') {
            steps {
                git branch: 'main', url: 'https://github.com/MonsterLtd/spring-with-maven.git'
            }
        }
        stage('Build') {
            steps {
                sh 'npm ci' //This is for building the nodejs project
            }
        }
        stage('Docker login') {
            steps {
                sh 'docker login -u $DOCKERHUB_CREDENTIALS_USR -p $DOCKERHUB_CREDENTIALS_PSW'
            }
        }
        stage('Test') {
            steps {
                sh 'npm test' //This is for testing the nodejs modules
            }
        }
        stage('Docker build and tag') {
            steps {
                sh 'docker build -t ${IMAGE_NAME} -f Dockerfile .'
                sh 'docker tag ${IMAGE_NAME} ${IMAGE_NAME}:${BUILD_NUMBER}'
            }
        }
        stage('Docker push') {
            steps {
                sh 'docker push ${IMAGE_NAME}:${BUILD_NUMBER}'
            }
        }

        stage('Deploy') {
           steps {
                // sh 'pkill node'
                // sh 'npm install -g forever'
                // sh 'forever start src/index.js'
                sh 'docker container rm -f mynodejsapp || true'
                sh 'docker container run -d -p 3000:3000 --name mynodejsapp ${IMAGE_NAME}'
           }
        }
    }
}