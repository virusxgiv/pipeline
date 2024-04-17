pipeline {
  agent any
  options {
    buildDiscarder(logRotator(numToKeepStr: '5'))
  }
  environment {
    HEROKU_API_KEY = credentials('heroku-api-key')
    IMAGE_NAME = 'givengoon/todo'
    IMAGE_TAG = 'latest'
    APP_NAME = 'todo'
  }
  stages {
    stage('Build') {
      steps {
        script {
          sh '/Applications/Docker.app/Contents/Resources/bin/docker build -t $IMAGE_NAME:$IMAGE_TAG .'
        }
      }
    }
    stage('Login') {
      steps {
        sh "echo $HEROKU_API_KEY | /Applications/Docker.app/Contents/Resources/bin/docker login --username=_ --password-stdin registry.heroku.com"
      }
    }
    stage('Push to Heroku registry') {
      steps {
        script {
          sh '''
            /Applications/Docker.app/Contents/Resources/bin/docker tag $IMAGE_NAME:$IMAGE_TAG registry.heroku.com/$APP_NAME/web
            /Applications/Docker.app/Contents/Resources/bin/docker push registry.heroku.com/$APP_NAME/web
          '''
        }
      }
    }
    stage('Release the image') {
      steps {
        sh '''
          heroku container:release web --app=$APP_NAME
        '''
      }
    }
  }
  post {
    always {
      sh '/Applications/Docker.app/Contents/Resources/bin/docker logout'
    }
  }
}
