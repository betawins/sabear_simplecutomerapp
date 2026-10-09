pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                echo "Building branch: ${env.BRANCH_NAME}"
            }
        }

        stage('Test') {
            steps {
                echo "Testing branch: ${env.BRANCH_NAME}"
            }
        }

        stage('Production Deploy') {
            when {
                branch 'master'
            }
            steps {
                echo 'Deploying to production...'
            }
        }

        stage('Staging Deploy') {
            when {
                branch 'develop'
            }
            steps {
                echo 'Deploying to staging...'
            }
        }
    }
}
