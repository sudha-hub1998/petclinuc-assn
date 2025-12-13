pipeline {
  agent { label 'agent-node' }

  environment {
    JAVA_HOME = "/usr/lib/jvm/java-17-openjdk-amd64"
    PATH = "${JAVA_HOME}/bin:${env.PATH}"
  }

  stages {
    stage('Checkout Code') {
      steps {
        checkout scm
        sh 'echo "Workspace contents:"'
        sh 'ls -la'
      }
    }
  }
}
