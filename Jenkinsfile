pipeline {
  agent { label 'agent-node' }

  environment {
    JAVA_HOME = "/usr/lib/jvm/java-17-openjdk-amd64"
    PATH = "${JAVA_HOME}/bin:${env.PATH}"
  }

  stages {
    stage('Build') {
      steps {
        sh 'java -version'
        sh 'mvn -version'
        sh 'mvn -B -DskipTests clean package'
      }
    }
  }
}


