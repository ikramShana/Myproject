
pipeline {
    agent any

    environment {
        AWS_REGION = "us-east-1"
        S3_BUCKET = "firsttime-project"                          // ✅ Fix 1: Removed trailing space
        CLOUDFRONT_DISTRIBUTION_ID = "EC9MB9L956EWG"
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm                                      // ✅ Fix 3: Dynamic branch checkout instead of hardcoded
            }
        }

        stage('Select Environment File') {
            steps {
                script {
                    def ENV_FILE

                    if (env.BRANCH_NAME == "ikramShana") {
                        ENV_FILE = ".env.production"
                    }
                    else if (env.BRANCH_NAME == "testing") {     // ✅ Fix 2: Changed duplicate "ikramShana" to "testing"
                        ENV_FILE = ".env.testing"
                    }
                    else {
                        ENV_FILE = ".env.local"
                    }

                    echo "Using environment file: ${ENV_FILE}"
                    sh "cp ${ENV_FILE} .env"
                }
            }
        }

        stage('Cleanup & Install') {
            steps {
                sh '''
                    echo "Cleaning old files and npm cache..."
                    rm -rf node_modules
                    npm cache clean --force

                    echo "Installing dependencies..."
                    npm ci
                '''
            }
        }

        stage('Build React App') {
            steps {
                sh 'npm run build'
            }
        }

        stage('Verify Build') {
            steps {
                sh 'ls -la dist'
            }
        }

        stage('Deploy to S3') {
            steps {
                withAWS(credentials: 'aws-access-key', region: "${AWS_REGION}") {   // ✅ Fix 4: Removed extra 's' in credentials ID
                    sh "aws s3 sync dist/ s3://${S3_BUCKET}/ --delete"
                }
            }
        }

        stage('CloudFront Invalidation') {
            steps {
                withAWS(credentials: 'aws-access-key', region: "${AWS_REGION}") {   // ✅ Fix 4: Removed extra 's' in credentials ID
                    sh "aws cloudfront create-invalidation --distribution-id ${CLOUDFRONT_DISTRIBUTION_ID} --paths '/*'"
                }
            }
        }
    }

    post {
        success {
            echo "SUCCESS: Frontend deployed to CloudFront!"
        }
        failure {
            echo "FAILURE: Build or Deployment failed."
        }
    }
}

