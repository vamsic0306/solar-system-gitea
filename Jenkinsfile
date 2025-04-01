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
                        dependencyCheck additionalArguments: '''
                            --scan \'./\' 
                            --out \'./\'  
                            --format \'ALL\' 
                            --disableYarnAudit \
                            --prettyPrint''', odcInstallation: 'OWASP-DepCheck-10'

                        dependencyCheckPublisher failedTotalCritical: 1, pattern: 'dependency-check-report.xml', stopBuild: false

                        publishHTML([allowMissing: true, alwaysLinkToLastBuild: true, icon: '', keepAll: true, reportDir: './', reportFiles: 'dependency-check-report.html', reportName: 'dependecy check html report', reportTitles: '', useWrapperFileDirectly: true])
                    }
                }
            }
        }
    }
    post {
        always {
           junit 'build/reports/**/*.xml'
            
        }
    }
}
