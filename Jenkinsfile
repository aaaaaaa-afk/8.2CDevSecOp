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
                powershell '''
                    # 1. Download SonarScanner CLI .zip if not downloaded
                    if (-not (Test-Path "sonar-scanner-cli.zip")) {
                        Write-Host "Downloading SonarScanner CLI..."
                        Invoke-WebRequest -Uri "https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-cli-5.0.1.3006-windows.zip" -OutFile "sonar-scanner-cli.zip"
                    }

                    # 2. Extract the archive
                    if (-not (Test-Path "sonar-scanner-5.0.1.3006-windows")) {
                        Write-Host "Extracting archive..."
                        Expand-Archive -Path "sonar-scanner-cli.zip" -DestinationPath "." -Force
                    }

                    # 3. Locate and invoke the SonarScanner executable
                    $SCANNER_EXE = Get-ChildItem -Path . -Filter "sonar-scanner.bat" -Recurse | Select-Object -ExpandProperty FullName
                    Write-Host "Invoking SonarScanner at: $SCANNER_EXE"
                    & $SCANNER_EXE -Dsonar.token="$env:SONAR_TOKEN"
                '''
            }
        }
    }
}