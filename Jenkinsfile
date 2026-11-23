pipeline {
    agent {
        docker {
            image 'semgrep/semgrep:latest'
            // Clear the image entrypoint and give Semgrep a writable HOME
            args '--entrypoint= -e HOME=/tmp'
        }
    }

    options {
        timeout(time: 30, unit: 'MINUTES')
        timestamps()
    }

    environment {
        REPORT = 'semgrep-report.json'
    }

    stages {
        stage('Semgrep Scan (OWASP Top 10)') {
            steps {
                sh 'git config --global --add safe.directory "$PWD" || true'
                sh 'semgrep --version'
                catchError(buildResult: 'UNSTABLE', stageResult: 'UNSTABLE') {
                    sh '''
                        semgrep scan \
                          --config p/owasp-top-ten \
                          --metrics=off \
                          --error \
                          --verbose \
                          --json-output="$REPORT"
                    '''
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
