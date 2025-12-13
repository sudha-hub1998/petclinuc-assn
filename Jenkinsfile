pipeline {
  agent { label 'agent-node' }

  environment {
    JAVA_HOME = "/usr/lib/jvm/java-17-openjdk-amd64"
    PATH = "${JAVA_HOME}/bin:${env.PATH}"
    TRIVY_SEVERITY = "HIGH,CRITICAL"
  }

  stages {

    stage('1-Checkout Code') {
      steps {
        checkout scm
      }
    }

    stage('2-Build & Test') {
      steps {
        sh 'mvn -B clean test'
      }
    }

    stage('3-Security Scan (Trivy)') {
      steps {
        sh '''
          set -e

          # Install Trivy if missing (fast)
          if ! command -v trivy >/dev/null 2>&1; then
            sudo apt-get update -y
            sudo apt-get install -y wget apt-transport-https gnupg lsb-release
            wget -qO - https://aquasecurity.github.io/trivy-repo/deb/public.key | sudo gpg --dearmor -o /usr/share/keyrings/trivy.gpg
            echo "deb [signed-by=/usr/share/keyrings/trivy.gpg] https://aquasecurity.github.io/trivy-repo/deb $(lsb_release -sc) main" | sudo tee /etc/apt/sources.list.d/trivy.list
            sudo apt-get update -y
            sudo apt-get install -y trivy
          fi

          # Scan repo filesystem; fail if HIGH/CRITICAL found
          trivy fs --quiet --exit-code 1 --severity ${TRIVY_SEVERITY} --ignore-unfixed .
        '''
      }
    }

  }
}
