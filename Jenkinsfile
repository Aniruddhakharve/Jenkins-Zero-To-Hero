pipeline {
    agent { label 'javapipe' }

    stages {
        stage('Checkout') {
            steps {
                echo 'Checking out code...'
                checkout scm
            }
        }

        stage('Build') {
            steps {
                echo 'Compiling Java program...'
                sh '''
                    mkdir -p out
                    javac -d out HelloWorld.java
                '''
            }
        }

        stage('Test') {
            steps {
                echo 'Running Test...'
                sh '''
                    if grep -q "public static void main" HelloWorld.java; then
                        echo "Main method exists - Test Passed"
                    else
                        echo "Test failed"
                        exit 1
                    fi
                '''
            }
        }

        stage('Deploy') {
            steps {
                echo 'Running Java app...'
                sh 'java -cp out HelloWorld'
            }
        }
    }
}

