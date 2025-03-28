pipeline {
    agent any

    stages {
        stage('parallel') {
            parallel {
                stage ("build") {
                    steps { 
                        echo "building"
                   }
                }
                stage ("TESTING") {
                    steps { 
                        echo "testing"
                   }
                }
            }
        }

        
        
    }
}