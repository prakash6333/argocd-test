pipeline {
    agent any

    environment {
        SONARQUBE = 'sonarqube'
        MAVEN_HOME = tool name: 'Maven', type: 'maven'
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/your-repo.git', credentialsId: 'git-creds'
            }
        }

        stage('Build with Maven') {
            steps {
                sh "${MAVEN_HOME}/bin/mvn clean package"
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonarqube') {
                    sh "${MAVEN_HOME}/bin/mvn sonar:sonar -Dsonar.projectKey=myproject"
                }
            }
        }

        stage('Quality Gate') {
            steps {
                timeout(time: 2, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }

        stage('Upload to Nexus') {
            steps {
                nexusArtifactUploader(
                    nexusVersion: 'nexus3',
                    protocol: 'http',
                    nexusUrl: 'localhost:8081',
                    groupId: 'com.mycompany.app',
                    version: '1.0.0',
                    repository: 'maven-releases',
                    credentialsId: 'nexus-creds',
                    artifacts: [[
                        artifactId: 'myapp',
                        classifier: '',
                        file: 'target/myapp.jar',
                        type: 'jar'
                    ]]
                )
            }
        }
    }

    post {
        success {
            echo '✅ Build, Test, and Deploy Completed Successfully!'
        }
        failure {
            echo '❌ Pipeline Failed!'
        }
    }
}
