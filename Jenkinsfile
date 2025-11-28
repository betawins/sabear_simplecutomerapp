pipeline {
    agent any

    tools {
        // Must match names from "Manage Jenkins → Global Tool Configuration"
        maven 'maven'
    }

    environment {
        // Nexus configuration
        NEXUS_VERSION = 'nexus3'
        NEXUS_PROTOCOL = 'http'
        NEXUS_URL = '3.92.251.66:8081'
        NEXUS_REPOSITORY = 'nexus'
        NEXUS_CREDENTIAL_ID = 'nexus'

        // SonarQube configuration
        SCANNER_HOME = tool 'sonar'  // Matches your Sonar Scanner installation name

        // Git configuration
        GIT_URL = 'https://github.com/Shaik123-hu/sabear_simplecutomerapp.git'
        GIT_BRANCH = 'feature-1.1'

        // App info
        APP_VERSION = '3.0'
        APP_NAME = 'SimpleCustomerApp'
    }

    stages {

        stage('Clone Code') {
            steps {
                git branch: "${GIT_BRANCH}", url: "${GIT_URL}"
                echo ":white_check_mark: Repository cloned from ${GIT_BRANCH}"
            }
        }

        stage('Maven Build') {
            steps {
                echo ":building_construction: Running Maven Build..."
                sh 'mvn -Dmaven.test.failure.ignore=true clean install'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                echo ":mag: Starting SonarQube Code Analysis..."

                // Use Jenkins credentials to securely inject SonarQube token
                withCredentials([string(credentialsId: 'sonar-token', variable: 'SONAR_TOKEN')]) {
                    sh '''
                        ${SCANNER_HOME}/bin/sonar-scanner \
                          -Dsonar.projectKey=Ncodeit \
                          -Dsonar.projectName=Ncodeit \
                          -Dsonar.projectVersion=${APP_VERSION} \
                          -Dsonar.sources=src \
                          -Dsonar.java.binaries=target \
                          -Dsonar.host.url=http://44.204.248.233:9000 \
                          -Dsonar.login=$SONAR_TOKEN
                    '''
                }

                echo ":white_check_mark: SonarQube scan triggered successfully."
            }
        }

        stage('Publish to Nexus') {
            steps {
                echo ":package: Uploading artifact to Nexus..."
                withCredentials([usernamePassword(credentialsId: "${NEXUS_CREDENTIAL_ID}", usernameVariable: 'NX_USER', passwordVariable: 'NX_PASS')]) {
                    sh '''
                        ARTIFACT=$(ls target/*.war | head -n 1)
                        echo "Found artifact: $ARTIFACT"
                        mvn deploy:deploy-file \
                          -DgroupId=com.javatpoint \
                          -DartifactId=${APP_NAME} \
                          -Dversion=${APP_VERSION} \
                          -Dpackaging=war \
                          -Dfile=$ARTIFACT \
                          -DrepositoryId=${NEXUS_CREDENTIAL_ID} \
                          -Durl=${NEXUS_PROTOCOL}://${NEXUS_URL}/repository/${NEXUS_REPOSITORY} \
                          -DgeneratePom=true \
                          -Dusername=$NX_USER \
                          -Dpassword=$NX_PASS
                    '''
                }
                echo ":white_check_mark: Artifact successfully published to Nexus"
            }
        }

        stage('Deploy to Tomcat') {
            steps {
                echo ":rocket: Deploying WAR file to Tomcat..."
                sh '''
                    WAR_FILE=$(ls target/*.war | head -n 1)
                    echo "Deploying $WAR_FILE to Tomcat..."
                    curl -T $WAR_FILE \
                         "http://54.145.245.39:8080/manager/text/deploy?path=/simplecustomerapp&update=true"
                '''
                echo ":white_check_mark: Deployment to Tomcat successful!"
            }
        }

        stage('Slack Notification') {
            steps {
                echo ":speech_balloon: Slack Notification Stage"
                script {
                    try {
                        slackSend(
                            channel: '#jenkins-integration',
                            color: '#36A64F',
                            message: ":white_check_mark: *${APP_NAME}* successfully built and deployed! \nJob: ${env.JOB_NAME} #${env.BUILD_NUMBER}"
                        )
                    } catch (err) {
                        echo ":warning: Slack plugin not installed or misconfigured: ${err.message}"
                    }
                }
            }
        }
    }

    post {
        failure {
            script {
                echo ":x: Build failed for Job: ${env.JOB_NAME} #${env.BUILD_NUMBER}"
                try {
                    slackSend(
                        channel: '#jenkins-integration',
                        color: '#FF0000',
                        message: ":x: Build failed for *${env.JOB_NAME}* #${env.BUILD_NUMBER}. Check Jenkins logs for details."
                    )
                } catch (err) {
                    echo ":warning: Slack failure message skipped: ${err.message}"
                }
            }
        }
    }
}
