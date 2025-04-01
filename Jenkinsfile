pipeline {
    agent any

    tools {
        nodejs 'NodeJS'  // Ensure "NodeJS" is configured in Jenkins Global Tools
    }

    stages {
        stage('Hello World') {
            steps {
                echo 'Hello, World! This is my first Jenkins Pipeline!'
            }
        }

        stage('Installing Dependencies') {
            options { timestamps() }
            steps {
                sh 'npm install --no-audit'
            }
        }

        stage('Dependencies Scanning') {
            parallel {
                stage('NPM Audit') {
                    steps {
                        script {
                            def exitCode = sh(script: 'npm audit --audit-level=high', returnStatus: true)
                            if (exitCode != 0) {
                                error "NPM Audit found vulnerabilities!"
                            }
                        }
                    }
                }
                
                stage('OWASP Dependency Check') {
                    steps {
                        sh '''
                            dependencyCheck additionalArguments: '--format HTML', odcInstallation: 'Dependency-Check'
                            dependency-check \
                            --scan ./ \
                            --outdir ./ \
                            --format ALL \
                            --pretty-print true
                        '''
                    }
                }
            }
        }
    }
}
