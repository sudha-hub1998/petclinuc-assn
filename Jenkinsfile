pipeline {
  agent { label 'agent-node' }

  environment {
    JAVA_HOME = "/usr/lib/jvm/java-17-openjdk-amd64"
    PATH = "${JAVA_HOME}/bin:${env.PATH}"
  }

  stages {
    stage('1-Checkout Code') {
      steps {
        checkout scm
      }
    }

    stage('2-Build & Test') {
      steps {
        sh 'echo "Java version:"'
        sh 'java -version'
        sh 'echo "Maven version:"'
        sh 'mvn -version'
        sh 'mvn -B clean test'
      }
    }
  }
}
