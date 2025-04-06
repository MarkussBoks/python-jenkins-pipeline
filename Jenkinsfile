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
                    testPython("GREETINGS", "dev", 7001)
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
                    testPython("GREETINGS", "staging", 7002)
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
                    testPython("GREETINGS", "preprod", 7003)
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
                    testPython("GREETINGS", "prod", 7004)
                    showLogs("AFTER PROD TEST")
                }
            }
        }
    }
}

// ---------- Izpildes funkcijas ----------

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

def testPython(testSet, envName, port) {
    echo "Testing ${testSet} on ${envName.toUpperCase()}..."

    bat """
        FOR /L %%i IN (1,1,5) DO (
            echo Attempt %%i to reach http://localhost:${port}/greetings
            curl http://localhost:${port}/greetings && EXIT /B 0
            timeout /t 2 >nul
        )
        EXIT /B 1
    """

    bat """
        if exist course-js-api-framework rmdir /s /q course-js-api-framework
        git clone https://github.com/mtararujs/course-js-api-framework
        cd course-js-api-framework
        call npm install
        call npm run greetings greetings_${envName}
    """
}

def showLogs(String message) {
    echo "----- PM2 STATUS (${message}) -----"
    bat "pm2 list"
}
