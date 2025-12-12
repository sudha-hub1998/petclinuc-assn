pipeline {
  agent { label 'maven-agent' }

  stages {
    stage('Checkout') {
      steps { checkout scm }
    }

    stage('Build') {
      steps { sh 'mvn -B -DskipTests clean package' }
    }
  }
}
