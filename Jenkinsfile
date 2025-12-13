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
        sh 'mvn -B clean test'
      }
    }

    stage('3-Security Scan (OWASP Dependency-Check)') {
      steps {
        sh '''
          set -e

          # Run OWASP Dependency-Check via Maven plugin.
          # - 'check' generates the report and evaluates vulnerabilities
          # - This command FAILS the pipeline if any dependency has CVSS score >= 7.0
          mvn -B org.owasp:dependency-check-maven:check -Dformat=HTML -DfailBuildOnCVSS=7

          echo "OWASP Dependency-Check report generated under: target/dependency-check-report.html"
        '''
      }
    }
  }
}
