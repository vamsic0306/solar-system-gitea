pipeline {
    agent any

    tools {
        nodejs 'NodeJS'  // Ensure "NodeJS" is configured in Jenkins Global Tools
    }

    environment {
  MANGODB_USERNAME = "1234567890"
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
                stage ("unit tstiing") {
                    steps {
                        withCredentials([usernamePassword(credentialsId: 'mango_useranme', passwordVariable: '1234567890fc', usernameVariable: 'mango_username')]) {
                        sh 'npm test'
                    }
                    junit allowEmptyResults: true, keepProperties: true, testResults: 'dependency-check-junit.xml'
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

                        junit allowEmptyResults: true, keepProperties: true, testResults: 'dependency-check-junit.xml'

                        publishHTML([allowMissing: true, alwaysLinkToLastBuild: true, icon: '', keepAll: true, reportDir: './', reportFiles: 'dependency-check-report.html', reportName: 'dependecy check html report', reportTitles: '', useWrapperFileDirectly: true])

                        
                    }
                }
                stage ("coverage") {
                    steps {
                        withCredentials([usernamePassword(credentialsId: 'mango_useranme', passwordVariable: '1234567890fc', usernameVariable: 'mango_username')]) {
                        sh 'npm run coverage'
                    }
                }
            }
        }
    }
    
}
