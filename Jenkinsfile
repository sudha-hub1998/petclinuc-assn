pipeline {
  agent { label 'agent-node' }

  environment {
    // Force Java 17 for this pipeline
    JAVA_HOME = "/usr/lib/jvm/java-17-openjdk-amd64"
    PATH = "${JAVA_HOME}/bin:${env.PATH}"

    // OWASP Dependency-Check cache location (speeds up future runs)
    ODC_DATA_DIR = "/home/ubuntu/odc-data"

    // Security threshold (keep 7 for strict gate; change to 10 if you want it to pass while learning next stages)
    ODC_FAIL_CVSS = "7"
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

    stage('3-Security Scan (OWASP - Learning Mode)') {
      steps {
        sh """
          set -e
          echo "OWASP Dependency-Check (LEARNING MODE): using cached DB, skipping updates"

          mvn -B org.owasp:dependency-check-maven:check \\
            -DfailBuildOnCVSS=${ODC_FAIL_CVSS} \\
            -DdataDirectory=${ODC_DATA_DIR} \\
            -DskipNVDUpdates=true \\
            -DcisaEnabled=false \\
            -DossindexAnalyzerEnabled=false

          echo "Report generated: target/dependency-check-report.html"
        """
      }
    }

  }
}
