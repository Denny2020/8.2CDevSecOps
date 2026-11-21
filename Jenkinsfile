// SIT753 6.1C - Part 1 Task 2: npm-based security tests over nodejs-goof.
pipeline {
    agent any

    options {
        skipDefaultCheckout(true) // Checkout stage below does the only clone
    }

    environment {
        SONAR_TOKEN           = credentials('SONAR_TOKEN') // masked in the console log
        SONAR_SCANNER_VERSION = '6.2.1.4610'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/Denny2020/8.2CDevSecOps.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('Run Tests') {
            steps {
                sh 'npm test || true' // Allows pipeline to continue despite test failures
            }
        }

        stage('Generate Coverage Report') {
            steps {
                // Ensure coverage report exists
                sh 'npm run coverage || true'
            }
        }

        stage('NPM Audit (Security Scan)') {
            steps {
                sh 'npm audit || true' // This will show known CVEs in the output
            }
        }

        stage('SonarCloud Analysis') {
            steps {
                sh '''
                    SCANNER_DIR="sonar-scanner-${SONAR_SCANNER_VERSION}-linux-x64"
                    if [ ! -d "$SCANNER_DIR" ]; then
                        curl -sSLo sonar-scanner.zip \
                          "https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-cli-${SONAR_SCANNER_VERSION}-linux-x64.zip"
                        unzip -q -o sonar-scanner.zip
                    fi
                    # -X prints the SonarCloud API calls and their HTTP 200 responses
                    "./$SCANNER_DIR/bin/sonar-scanner" -X
                '''
            }
        }
    }
}
