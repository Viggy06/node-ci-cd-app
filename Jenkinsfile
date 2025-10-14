pipeline {
    agent any

    parameters {
        string(name: 'gitCredentialsId', defaultValue: 'march-2025', description: 'Git credentials for GitHub')
    }

    stages {
        stage('Clone Git') {
            steps {
                // Clone the GitHub repository
                git branch: 'main',
                    credentialsId: params.gitCredentialsId,
                    url: 'https://github.com/Viggy06/devops_tools.git'

                // Display current directory and files for debugging
                sh 'pwd'
                sh 'ls -lrth'
            }
        }

        stage('Echo Message') {
            steps {
                // Simple echo message
                sh 'echo "Hello, Jenkins!"'
            }
        }
    }
}
