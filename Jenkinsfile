pipeline {
    agent any

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/prakash6333/argocd-test.git'
            }
        }

        stage('Validate YAML') {
            steps {
                sh '''
                    pip install yamllint
                    yamllint .
                '''
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonarqube') {
                    sh 'sonar-scanner -Dsonar.projectKey=argocd-configs'
                }
            }
        }
    }

    post {
        success {
            echo '✅ YAML validated and SonarQube scan completed successfully!'
        }
        failure {
            echo '❌ Pipeline failed — check logs.'
        }
    }
}
