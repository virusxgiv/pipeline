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
    DOCKER_EXEC = '/Applications/Docker.app/Contents/Resources/bin/docker'
  }
  stages {
    stage('Build') {
      steps {
        sh "$DOCKER_EXEC build -t $IMAGE_NAME:$IMAGE_TAG ."
      }
    }
    stage('Login') {
      steps {
        sh "echo $HEROKU_API_KEY | $DOCKER_EXEC login --username=_ --password-stdin registry.heroku.com"
      }
    }
    stage('Push to Heroku registry') {
      steps {
        sh '''
          $DOCKER_EXEC tag $IMAGE_NAME:$IMAGE_TAG registry.heroku.com/$APP_NAME/web
          $DOCKER_EXEC push registry.heroku.com/$APP_NAME/web
        '''
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
      sh '$DOCKER_EXEC logout'
    }
  }
}
