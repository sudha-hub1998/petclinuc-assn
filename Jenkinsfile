pipeline {
  agent { label 'agent-maven-node' }

  stages {
    stage('Checkout') {
      steps { checkout scm }
    }

    stage('Build') {
      steps { sh 'mvn -B -DskipTests clean package' }
    }
  }
}
