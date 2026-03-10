
peline {
    agent any
 
    environment {
        AWS_REGION = 'us-east-1'
        S3_BUCKET = 'firsttime-project'
        CLOUDFRONT_DISTRIBUTION_ID = 'EC9MB9L956EWG'
    }
 
    stages {
        stage('checkout') {
            steps {
                git credentialsId: 'github-cred', url: 'https://github.com/ikramShana/Myproject.git'
            }
        }
 
        stage('install npm dependencies') {
            steps {
 {               sh 'npm install'
            }
        }
        stage('npm run build'){
            steps {
                sh 'npm run build'
            }
        }
        stage ('move build satic files to s3 bucket'){
            steps {
                withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', credentialsId: 'aws-accesss-key']]) {
                sh "/usr/local/bin/aws s3 sync frontend/build/ s3://${S3_BUCKET} --delete"
 
                }
            }
 
        }
        stage('invalidate cloudfront cache'){
            steps{
                withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', credentialsId: 'aws-accesss-key']]) {
                sh "/usr/local/bin/aws cloudfront create-invalidation --distribution-id ${CLOUDFRONT_DISTRIBUTION_ID} --paths '/*'"
                }
            }
        }
 
    }
 
}

















































