pipeline {
    agent {
        docker {
            image 'semgrep/semgrep:latest'
            args '--entrypoint= -e HOME=/tmp'
        }
    }
    options { timeout(time: 30, unit: 'MINUTES') }
    stages {
        stage('Semgrep (SAST)') {
            steps {
                sh 'git config --global --add safe.directory "$PWD" || true'
                sh 'semgrep scan --config p/default --metrics=off --error --verbose'
            }
        }
    }
}
