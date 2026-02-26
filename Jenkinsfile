pipeline {

    agent any

    environment {
        PROJECT_DIR = "Password Protection"
        JUNIT_JAR = "junit-platform-console-standalone.jar"
    }

    stages {

        stage('Checkout') {
            steps {
                echo "Cloning repository..."
                checkout scm
            }
        }

        stage('Build') {
            steps {
                sh '''
                echo "Starting Build Stage..."

                cd "$PROJECT_DIR"

                mkdir -p build

                javac -d build src/*.java

                echo "Build completed successfully."
                '''
            }
        }

        stage('Test') {
            steps {
                sh '''
                echo "Starting Test Stage..."

                cd "$PROJECT_DIR"

                # Download JUnit if not present
                if [ ! -f $JUNIT_JAR ]; then
                    echo "Downloading JUnit..."
                    curl -L -o $JUNIT_JAR \
                    https://repo1.maven.org/maven2/org/junit/platform/junit-platform-console-standalone/1.10.0/junit-platform-console-standalone-1.10.0.jar
                fi

                # Compile test classes if test directory exists
                if [ -d test ]; then
                    mkdir -p test-build
                    javac -cp $JUNIT_JAR:build -d test-build test/*.java

                    # Run JUnit tests
                    java -jar $JUNIT_JAR \
                    --class-path build:test-build \
                    --scan-class-path

                    echo "JUnit tests executed successfully."
                else
                    echo "No test directory found. Skipping test stage."
                fi
                '''
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                echo "Starting Deploy Stage..."

                cd "$PROJECT_DIR"

                jar cf FileEncrypter.jar -C build .

                echo "Deployment completed. Artifact created: FileEncrypter.jar"
                '''
            }
        }
    }

    post {

        success {
            echo "Pipeline executed successfully!"
        }

        failure {
            echo "Pipeline failed! Check console output for errors."
        }

        always {
            echo "Pipeline execution finished."
        }
    }
}
