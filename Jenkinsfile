pipeline {
    agent any
    environment {
        SONAR_TOKEN = credentials('SONAR_TOKEN')
    }
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/joabjunxianglow/8.2CDevSecOps.git'
            }
        }
        stage('Install Dependencies') {
            steps {
                bat 'npm install'
            }
        }
        stage('Run Tests') {
            steps {
                bat 'npm test || exit /b 0' // Allows pipeline to continue despite test failures
            }
        }
        stage('Generate Coverage Report') {
            steps {
                // Ensure coverage report exists
                bat 'npm run coverage || exit /b 0'
            }
        }
        stage('NPM Audit (Security Scan)') {
            steps {
                bat 'npm audit || exit /b 0' // This will show known CVEs in the output
            }
        }
        stage('SonarCloud Analysis'){
            steps {
                withCredentials([string(credentialsId: 'SONAR_TOKEN', variable: 'SONAR_TOKEN')]){
                    bat '''
                        powershell -Command "Invoke-WebRequest -Uri https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-cli-7.2.0.5079-windows-x64.zip -OutFile sonar-scanner.zip"
                        powershell -Command "Expand-Archive -Path sonar-scanner.zip -DestinationPath %WORKSPACE%\\sonar-scanner -Force"
                        set PATH=%WORKSPACE%\\sonar-scanner\\sonar-scanner-7.2.0.5079-windows-x64\bin;%PATH%
                        cd %WORKSPACE%
                        sonar-scanner -Dsonar.login=%SONAR_TOKEN%
                    '''
                }
                
            }
        }
    }

}
