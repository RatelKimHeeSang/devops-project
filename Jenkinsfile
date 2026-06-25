pipeline {
    agent any
    environment {
        IMAGE_NAME = "khsandbox/flask-devops"
    }
    stages {
        stage('Pull Code') {
            steps {
                git branch: 'main', url: 'https://github.com/RatelKimHeeSang/devops-project.git'
            }
        }
        stage('OWASP Dependency Check') {
            steps {
                dependencyCheck additionalArguments: '--scan ./'
            }
        }
        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonarqube') {
                    sh '''
                    sonar-scanner \
                    -Dsonar.projectKey=flask-app \
                    -Dsonar.sources=. \
                    -Dsonar.host.url=$SONAR_HOST_URL\
                    -Dsonar.token=$SONAR_AUTH_TOKEN
                    '''
                }
            }
        }
        stage('Docker Build') {
            steps {
                sh 'docker build -t $IMAGE_NAME:$BUILD_NUMBER .'
            }
        }
        stage('Trivy Scan') {
            steps {
                sh 'trivy image $IMAGE_NAME:$BUILD_NUMBER'
            }
        }
        stage('Docker Push') {
            steps {
                withDockerRegistry([credentialsId: 'dockerhub']) {
                    sh 'docker push $IMAGE_NAME:$BUILD_NUMBER'
                }
            }
        }
    }
}
