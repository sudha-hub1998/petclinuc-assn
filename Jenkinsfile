pipeline {
  agent { label 'agent-node' }

  stages {
    stage('Build') {
      steps {
        sh 'mvn -B -DskipTests clean package'
      }
    }
  }
}

