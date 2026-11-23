pipeline {
    agent {
        docker {
            image 'semgrep/semgrep:latest'
            args '--entrypoint= -e HOME=/tmp -e SEMGREP_IN_DOCKER=0'
        }
    }

    options {
        timeout(time: 30, unit: 'MINUTES')
        timestamps()
    }

    environment {
        REPORT = 'semgrep-report.json'
        SCAN_PATHS = '.'   // space-separated paths, '.' = whole repo checkout
    }

    stages {
        stage('Semgrep Scan (OWASP Top 10)') {
            steps {
                sh 'git config --global --add safe.directory "$PWD" || true'
                sh 'semgrep --version'

                // Exit code 1 = findings found, 2 = Semgrep itself failed
                script {
                    int rc = sh(returnStatus: true, script: '''
                        semgrep scan \
                          --config p/owasp-top-ten \
                          --metrics=off \
                          --error \
                          --verbose \
                          --json-output="$REPORT" \
                          $SCAN_PATHS
                    ''')
                    if (rc == 1) {
                        unstable('Semgrep found issues')
                    } else if (rc != 0) {
                        error("Semgrep failed with exit code ${rc}")
                    }
                }
            }
        }
    }

    post {
        always {
            archiveArtifacts artifacts: 'semgrep-report.json', allowEmptyArchive: true, fingerprint: true
        }
    }
}
