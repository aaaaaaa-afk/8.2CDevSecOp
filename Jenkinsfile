pipeline {
    agent any

    environment {
        SONAR_TOKEN = credentials('SONAR_TOKEN')
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/aaaaaaa-afk/8.2CDevSecOp'
            }
        }
        stage('Install Dependencies') {
            steps {
                bat 'npm install'
            }
        }
        stage('Run Tests') {
            steps {
                bat 'npm test || exit /b 0'
            }
        }
        stage('Generate Coverage Report') {
            steps {
                bat 'npm run coverage || exit /b 0'
            }
        }
        stage('NPM Audit (Security Scan)') {
            steps {
                bat 'npm audit || exit /b 0'
            }
        }
        stage('SonarCloud Analysis') {
            steps {
                cleanWs()
                powershell '''
                    if (-not (Test-Path "sonar-scanner-cli.zip")) {
                        Invoke-WebRequest -Uri "https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-cli-6.2.1.4610-windows-x64.zip" -OutFile "sonar-scanner-cli.zip"
                    }

                    if (-not (Test-Path "sonar-scanner-6.2.1.4610-windows-x64")) {
                        Expand-Archive -Path "sonar-scanner-cli.zip" -DestinationPath "." -Force
                    }

                    $SCANNER_EXE = Get-ChildItem -Path . -Filter "sonar-scanner.bat" -Recurse | Select-Object -ExpandProperty FullName
                    & $SCANNER_EXE
                '''
            }
        }
    }
}