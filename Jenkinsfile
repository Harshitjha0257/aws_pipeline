pipeline {
  agent any

  environment {
    AWS_REGION = 'ap-south-1'
    BACKEND_BUCKET = 'aws-pipeline-backend-harshit'
    FRONTEND_BUCKET = 'aws-pipeline-frontend-harshit'
  }

  stages {
    stage('Clone Repo') {
      steps {
        echo '✅ Cloning the GitHub repository...'
        checkout scm
      }
    }

    stage('Build Backend') {
      steps {
        echo '🔧 Building Java backend with Gradle...'
        dir('backend') {
          sh './gradlew build'
        }
      }
    }

    stage('Build Frontend') {
      steps {
        echo '🎨 Building React frontend...'
        dir('frontend') {
          sh 'npm install'
          sh 'npm run build'
        }
      }
    }

    stage('Upload to S3') {
      steps {
        echo '☁️ Uploading artifacts to S3...'
        withCredentials([[
          $class: 'AmazonWebServicesCredentialsBinding',
          credentialsId: 'aws-jenkins'
        ]]) {
          dir('backend/build/libs') {
            sh 'aws s3 cp . s3://$BACKEND_BUCKET/ --recursive --region $AWS_REGION'
          }
          dir('frontend/build') {
            sh 'aws s3 cp . s3://$FRONTEND_BUCKET/ --recursive --region $AWS_REGION'
          }
        }
      }
    }
  }

  post {
    success {
      echo '✅ Deployment to S3 completed successfully!'
    }
    failure {
      echo '❌ Deployment failed. Check logs.'
    }
  }
}

