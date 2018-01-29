pipeline {
  agent {
    label "prod"
  }
  options {
    buildDiscarder(logRotator(numToKeepStr: '2'))
    disableConcurrentBuilds()
  }
  stages {
    stage("notify") {
      steps {
        slackSend(
          color: "info",
          message: "${env.JOB_NAME} started: ${env.RUN_DISPLAY_URL}"
        )
      }
    }
    stage("deploy") {
      when {
        branch "master"
      }
      environment {
        PORTAINER_DOMAIN = "${env.portainerDomain}"
      }
      steps {
        script {
          if (env.portainerDomain) {
            sh "docker stack deploy -c stack.yml portainer"
          } else {
            sh 'echo "ERROR: env.portainerDomain is required." && exit 1'
          }
        }
      }
    }
  }
  post {
    always {
      sh "docker system prune -f"
    }
    failure {
      slackSend(
        color: "danger",
        message: "${env.JOB_NAME} failed: ${env.RUN_DISPLAY_URL}"
      )
    }
    success {
      slackSend(
        color: "good",
        message: "${env.JOB_NAME} succeeded: ${env.RUN_DISPLAY_URL}"
      )
    }
  }
}