pipeline {
    agent any
    environment {
        DOCKER_IMAGE = "mmk20001976/scientific-calculator"   // Change to your Docker Hub repository
        DOCKER_USERNAME = "mmk20001976" // Change to your Jenkins Docker credentials ID
	DOCKER_PASSWORD = "Messi@10500"
    }

    stages {

	stage('Setup Permissions') {
            steps {
                script {
                    sh '''
            echo "Granting permissions to the Jenkins user.."
            sudo usermod -aG docker jenkins
            sudo mkdir -p /var/lib/jenkins/.ssh
            sudo chown -R jenkins:jenkins /var/lib/jenkins/.ssh
            sudo chmod 700 /var/lib/jenkins/.ssh
                  '''
                }
            }
        }	

        stage('Checkout') {
            steps {
                // Checkout the code from GitHub repository
                git branch:'master', url:'https://github.com/mmk20001976/Spe-Mini-Project.git'
            }
        }

        stage('Build') {
            steps {
                // Run Maven clean and install
                sh 'mvn clean install'
            }
        }

        stage('Test') {
            steps {
                // Run Maven tests (JUnit tests)
                sh 'mvn test'
            }
        }

        stage('Docker Build') {
            steps {
                // Build Docker image
		sh 'ls -l'
		sh 'sudo docker build -t ${DOCKER_IMAGE} .'
            }
        }

        stage('Docker Push') {
            steps {
                // Docker login using Jenkins credentials
		sh '''
                    echo "Logging in to Docker Hub..."
                    echo "${DOCKER_PASSWORD}" | sudo docker login -u "${DOCKER_USERNAME}" --password-stdin
                    sudo docker push ${DOCKER_IMAGE}
                    '''
            }
        }
	
	 stage('Run Ansible Deployment') {
            steps {
                sh '''
                    echo "Creating Ansible inventory file..."
                    echo "[myhosts]" > inventory.ini
                    echo "localhost ansible_connection=local ansible_become_pass=123" >> inventory.ini

                    echo "Running Ansible Playbook..."
		    ansible-playbook -i inventory.ini deploy_calculator.yml --extra-vars "ansible_become_pass=123" -vvvv
                '''
            }
        }

    }

    post {
        success {
            echo 'Build and push succeeded!'
        }
        failure {
            echo 'Build or push failed!'
        }
    }
}
