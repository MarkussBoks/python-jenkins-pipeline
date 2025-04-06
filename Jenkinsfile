pipeline {
    agent any

    stages {
        stage('Install pip dependencies') {
            steps {
                script {
                    installPipDeps()
                }
            }
        }

        stage('Deploy to DEV') {
            steps {
                script {
                    showLogs("BEFORE DEV DEPLOY")
                    deployPython("DEV", 7001)
                    showLogs("AFTER DEV DEPLOY")
                }
            }
        }
        stage('Test on DEV') {
            steps {
                script {
                    testPython("GREETINGS", "DEV", 7001)
                    showLogs("AFTER DEV TEST")
                }
            }
        }

        stage('Deploy to STAGING') {
            steps {
                script {
                    showLogs("BEFORE STAGING DEPLOY")
                    deployPython("STAGING", 7002)
                    showLogs("AFTER STAGING DEPLOY")
                }
            }
        }
        stage('Test on STAGING') {
            steps {
                script {
                    testPython("GREETINGS", "STAGING", 7002)
                    showLogs("AFTER STAGING TEST")
                }
            }
        }

        stage('Deploy to PREPROD') {
            steps {
                script {
                    showLogs("BEFORE PREPROD DEPLOY")
                    deployPython("PREPROD", 7003)
                    showLogs("AFTER PREPROD DEPLOY")
                }
            }
        }
        stage('Test on PREPROD') {
            steps {
                script {
                    testPython("GREETINGS", "PREPROD", 7003)
                    showLogs("AFTER PREPROD TEST")
                }
            }
        }

        stage('Deploy to PROD') {
            steps {
                script {
                    showLogs("BEFORE PROD DEPLOY")
                    deployPython("PROD", 7004)
                    showLogs("AFTER PROD DEPLOY")
                }
            }
        }
        stage('Test on PROD') {
            steps {
                script {
                    testPython("GREETINGS", "PROD", 7004)
                    showLogs("AFTER PROD TEST")
                }
            }
        }
    }
}

def installPipDeps() {
    echo "Installing pip dependencies for python-greetings..."
    bat '''
    if exist python-greetings rmdir /s /q python-greetings
    git clone https://github.com/mtararujs/python-greetings.git
    pip install -r python-greetings\\requirements.txt
    '''
}

def deploy(envName, port) {
    stage("Deploy to ${envName.toUpperCase()}") {
        script {
            echo "----- PM2 STATUS (BEFORE ${envName.toUpperCase()} DEPLOY) -----"
            bat "pm2 list"

            echo "Deploying to ${envName.toUpperCase()} on port ${port}..."

            bat """
                cd python-greetings
                echo Checking current directory...
                cd
                dir

                echo Checking app.py exists...
                dir app.py

                echo PM2 location and version check...
                where pm2
                pm2 -v

                echo Deleting old PM2 process for ${envName}...
                pm2 delete python-greetings-${envName.toUpperCase()} || exit /B 0

                echo Starting new PM2 process...
                pm2 start app.py --name "python-greetings-${envName.toUpperCase()}" --interpreter python -- --port=${port}

                echo Waiting a few seconds before checking logs...
                timeout /t 3 >nul

                echo PM2 process list after deploy:
                pm2 list

                echo Last 50 log lines:
                pm2 logs python-greetings-${envName.toUpperCase()} --lines 50
            """
        }
    }
}

def testPython(String test_set, String environment, int port) {
    echo "Testing ${test_set} on ${environment}..."
    bat """
    ping 127.0.0.1 -n 4 >nul
    curl http://localhost:${port}/greetings
    """
}

def showLogs(String message) {
    echo "----- PM2 STATUS (${message}) -----"
    bat "pm2 list"
}
