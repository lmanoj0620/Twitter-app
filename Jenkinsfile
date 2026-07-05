pipeline{
    agent any
    
    environment {
        Image_Name = "lmanojbalaji/manojbala:${GIT_COMMIT}"
    }
    tools
    {
        jdk 'java-17'
        maven 'maven'
    }

    stages {
        stage('Git-checkout') {
            steps {
                git url: 'https://github.com/lmanoj0620/Twitter-app.git', branch: 'main'
            }
        }
        stage('Maven-Compile') {
            steps {
                sh ''' mvn clean compile '''
            }
        }
        stage("Maven-Test") {
            steps {
                sh ''' mvn clean test '''
            }
        }
        stage('Maven-Package') {
            steps {
                sh ''' mvn clean package '''
            }
        }
        stage('Building-Stage') {
            steps {
                sh '''
                    printenv
                    docker build -t ${Image_Name} .
                '''
            }
        }
        stage("Docker-Login") {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'docker-hub-creds',
                        usernameVariable: 'DOCKER_USERNAME',
                        passwordVariable: 'DOCKER_PASSWORD'
                    )
                ]) {
                    sh 'echo $DOCKER_PASSWORD | docker login -u $DOCKER_USERNAME --password-stdin'
                }
            }
        }
        stage('Pushing to Docker hub') {
            steps {
                sh '''
                    docker push ${Image_Name}
                '''
            }
        }
        stage("Deploy to microk8s") {
            steps {
                sh "microk8s.kubectl apply -f deploy.yaml"
            }
        }
        
    }

}
