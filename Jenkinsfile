pipeline {
  agent { label 'Built-In-Node' }

  stages {
    stage('Checkout') {
      steps { checkout scm }
    }

    stage('Build') {
      steps { sh 'mvn -B -DskipTests clean package' }
    }
  }
}
