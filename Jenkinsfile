pipeline {
    agent any

    stages {
        stage('install-pip-deps') {
            steps {
                echo 'Cloning python-greetings repo...'
                bat 'if exist python-greetings rmdir /s /q python-greetings'
                bat 'git clone https://github.com/mtararujs/python-greetings.git'
                bat 'dir python-greetings'
                bat 'pip install -r python-greetings\\requirements.txt'
            }
        }

        stage('deploy-to-dev') {
            steps {
                echo 'Deploying python-greetings to dev on default port 3000...'
                bat '''
                cd python-greetings
                pm2 delete python-greetings-dev || exit 0
                pm2 start app.py --name python-greetings-dev --interpreter python
                pm2 logs python-greetings-dev --lines 10
                '''
            }
        }


        stage('test-on-dev') {
            steps {
                echo 'Waiting for service on port 3000...'
                bat 'ping 127.0.0.1 -n 4 >nul'
                echo 'Testing service on port 3000...'
                bat 'curl http://localhost:3000/greetings'
            }
        }
    }
}
