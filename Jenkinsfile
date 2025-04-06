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
                    echo "Deploying to DEV"
                    bat '''
                        pm2 delete python-greetings-DEV || exit 0
                        cd python-greetings
                        pm2 start app.py --name python-greetings-DEV --interpreter="D:/Python/python.exe" -- --port=7001
                    '''
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

def deployPython(envName, port) {
    echo "Deploying to ${envName}"
    bat """
    pm2 delete python-greetings-${envName} || exit 0
    cd python-greetings
    pm2 start app.py --name python-greetings-${envName} --interpreter="D:/Python/python.exe" -- --port=${port}
    ping 127.0.0.1 -n 6 >nul
    """
}

def testPython(String test_set, String environment, int port) {
    echo "Testing ${test_set} on ${environment}..."
    bat """
    FOR /L %%i IN (1,1,5) DO (
        echo Attempt %%i to reach http://localhost:${port}/greetings
        curl http://localhost:${port}/greetings && EXIT /B 0
        timeout /t 2 >nul
    )
    EXIT /B 1
    """
}


def showLogs(String message) {
    echo "----- PM2 STATUS (${message}) -----"
    bat "pm2 list"
}
