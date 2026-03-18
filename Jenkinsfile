pipeline {
    agent any
    stages {
        stage('code quality') {
            steps {
                echo 'running code analysis...'
                withSonarQubeEnv('sonarqube') {
                    withMaven(maven: 'maven-3.9') {
                        sh 'mvn sonar:sonar'
                    }
                }
            }
        }
        stage('build jar') {
            steps {
                echo 'building the application...'
                withMaven(maven: 'maven-3.9') {
                    sh 'mvn package'
                }
            }
        }
        stage('build image') {
            steps {
                echo 'building the docker image and scanning...'
                withCredentials([usernamePassword(credentialsId: 'docker-hub0repo-credentials', passwordVariable: 'PASS', usernameVariable: 'USER')]) {
                    sh 'docker build -t tomiwa97/docker_app:jma-1.1 .'
                    sh 'trivy image --severity HIGH,CRITICAL --exit-code 1 tomiwa97/docker_app:jma-1.1'
                    sh "echo $PASS | docker login -u $USER --password-stdin"
                    sh 'docker push tomiwa97/docker_app:jma-1.1'
                }
            }
        }
    }
}
