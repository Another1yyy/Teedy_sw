pipeline {
  agent any

  options {
    timestamps()
  }

  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('Clean') {
      steps {
        script {
          runMaven('mvn -B -ntp clean')
        }
      }
    }

    stage('Compile') {
      steps {
        script {
          runMaven('mvn -B -ntp compile')
        }
      }
    }

    stage('Test') {
      steps {
        script {
          runMaven('mvn -B -ntp "-Dmaven.test.failure.ignore=true" test')
        }
      }
      post {
        always {
          junit allowEmptyResults: true, testResults: '**/target/surefire-reports/*.xml'
        }
      }
    }

    stage('PMD') {
      steps {
        script {
          runMaven('mvn -B -ntp pmd:pmd')
        }
      }
    }

    stage('Site') {
      steps {
        script {
          runMaven('mvn -B -ntp site')
        }
      }
    }

    stage('Package') {
      steps {
        script {
          runMaven('mvn -B -ntp "-DskipTests" package')
        }
      }
    }
  }

  post {
    always {
      archiveArtifacts allowEmptyArchive: true, artifacts: '**/target/*.jar, **/target/*.war, target/site/**'
    }
  }
}

def runMaven(String command) {
  if (isUnix()) {
    sh command
  } else {
    bat command
  }
}
