pipeline {
    agent any

    stages {
        stage('install-pip-deps') {
            steps {
                echo 'Cloning python-greetings repo...'
                bat 'rmdir /s /q python-greetings'
                bat 'git clone https://github.com/mtararujs/python-greetings.git'
                bat 'dir python-greetings'
                bat 'pip install -r python-greetings\\requirements.txt'
            }
        }
    }
}
