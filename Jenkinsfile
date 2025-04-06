pipeline {
    agent any

    stages {
        stage('install-pip-deps') {
            steps {
                echo 'Cloning python-greetings repo...'
                bat 'rm -rf python-greetings'

                bat 'git clone https://github.com/mtararujs/python-greetings.git'

                echo 'Listing cloned files...'
                bat 'ls python-greetings'

                echo 'Installing dependencies...'
                bat 'pip install -r python-greetings/requirements.txt'
            }
        }
    }
}
