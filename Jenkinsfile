pipeline {
  agent { label 'agent-node' }

  environment {
    JAVA_HOME = "/user/lib/jvm/java-17-openjdk-amd64"
    PATH = "${JAVA_HOME}/bin:${env.PATH}"

    TRIVY_SEVERITY = "HIGH,CRITICAL"

    // ---- Deploy settings ----
    APP_HOST    = "10.0.1.254"
    APP_DIR     = "/opt/petclinic"
    APP_SERVICE = "petclinic"
  }

  stages {

    stage('1-Checkout Code') {
      steps {
        checkout scm
      }
    }

    stage('2-Build & Test') {
      steps {
        sh 'java -version'
        sh 'mvn -version'
        sh 'mvn -B clean test'
      }
    }

    stage('3-Security Scan (Trivy)') {
      steps {
        sh '''
          set -e
          if ! command -v trivy >/dev/null 2>&1; then
            sudo apt-get update -y
            sudo apt-get install -y wget apt-transport-https gnupg lsb-release
            wget -qO - https://aquasecurity.github.io/trivy-repo/deb/public.key | sudo gpg --dearmor -o /usr/share/keyrings/trivy.gpg
            echo "deb [signed-by=/usr/share/keyrings/trivy.gpg] https://aquasecurity.github.io/trivy-repo/deb $(lsb_release -sc) main" | sudo tee /etc/apt/sources.list.d/trivy.list
            sudo apt-get update -y
            sudo apt-get install -y trivy
          fi

          trivy fs --quiet --exit-code 1 --severity ${TRIVY_SEVERITY} --ignore-unfixed .
        '''
      }
    }

    stage('4-Package') {
      steps {
        sh 'mvn -B -DskipTests package'
        sh 'ls -la target || true'
      }
    }

    stage('5-Archive Artifact') {
      steps {
        archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
      }
    }

    stage('6-Deploy to App Server (main only)') {
      when { branch 'main' }

      steps {
        withCredentials([
          sshUserPrivateKey(
            credentialsId: 'app-server',      // ✅ your Jenkins credential ID
            keyFileVariable: 'SSH_KEY',
            usernameVariable: 'SSH_USER'
          )
        ]) {
          sh '''
            set -e

            # Pick the first jar from target/
            JAR=$(ls -1 target/*.jar | head -n 1)

            echo "Deploying $JAR to ${SSH_USER}@${APP_HOST}:${APP_DIR}/app.jar"

            # Copy jar to app server
            scp -i "$SSH_KEY" -o StrictHostKeyChecking=no "$JAR" \
              "${SSH_USER}@${APP_HOST}:${APP_DIR}/app.jar"

            # Restart service on app server
            ssh -i "$SSH_KEY" -o StrictHostKeyChecking=no "${SSH_USER}@${APP_HOST}" \
              "sudo systemctl daemon-reload && sudo systemctl restart ${APP_SERVICE} && sudo systemctl status ${APP_SERVICE} --no-pager -l"
          '''
        }
      }
    }
  }
}
