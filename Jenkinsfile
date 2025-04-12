pipeline {
    agent any

    environment {
        ANSIBLE_HOST_KEY_CHECKING = "False"  // Disable host key checking (for automation)
    }

    stages {
        stage('Checkout') {
            steps {
                // Checkout the repository (if using SCM)
                git 'https://github.com/your-repo/your-project.git'
            }
        }

        stage('Install Ansible') {
            steps {
                script {
                    // Install Ansible inside the Jenkins container (if necessary)
                    sh 'docker exec ansible-container apt-get update && apt-get install -y ansible'
                }
            }
        }

        stage('OWASP Dependency Check') {
            steps {
                script {
                    // Run OWASP Dependency-Check to check for vulnerabilities in dependencies
                    sh """
                    wget https://github.com/jeremylong/DependencyCheck/releases/download/v6.5.0/dependency-check-6.5.0-release.zip
                    unzip dependency-check-6.5.0-release.zip -d dependency-check
                    cd dependency-check
                    ./bin/dependency-check.sh --project my-project --scan /path/to/your/project --out /path/to/report/directory
                    """
                }
            }
        }

        stage('Run Ansible Playbook') {
            steps {
                script {
                    // Run the Ansible playbook using the Ansible container
                    sh 'docker exec ansible-container ansible-playbook /home/ansible/deploy.yml -i "remote_host,"'
                }
            }
        }

        stage('Deploy to Server') {
            steps {
                script {
                    // Perform deployment on remote server via Ansible
                    sh 'docker exec ansible-container ansible-playbook /home/ansible/deploy.yml -i "remote_host,"'
                }
            }
        }
    }

    post {
        success {
            echo 'Deployment successful!'
        }
        failure {
            echo 'Deployment failed!'
        }
    }
}


/*
Step-by-Step Explanation of the Added Email Notification
1.	Environment Variables:
o	EMAIL_RECIPIENTS: An environment variable is defined in the environment block to hold the recipient email address. You can add multiple recipients by separating email addresses with commas (e.g., email1@example.com,email2@example.com).
2.	Email Configuration in post Section:
o	success: When the pipeline succeeds (i.e., the playbook runs successfully), an email is sent to the specified recipient(s) with the subject "SUCCESS" and a body message indicating that the deployment was successful. It also includes a link to the build details.
o	failure: If the pipeline fails (e.g., an error occurs during playbook execution), an email is sent with the subject "FAILURE" and a body message that provides a link to the build URL where you can review the logs and troubleshoot.
o	unstable: This block sends an email when the build is marked as unstable, typically due to failing tests or other issues that don't cause a complete failure.
o	always: This block will always run after the build, regardless of success or failure, which you can use for additional tasks (like cleanup or a final notification).
3.	The emailext Function:
o	The emailext function is used to send the email. It takes the following parameters:
	subject: The subject of the email (e.g., SUCCESS: Build XYZ).
	body: The content of the email, which can contain build details, logs, or URLs.
	to: The recipient's email address (the EMAIL_RECIPIENTS environment variable is used here).
Step 4: Email Configuration on Jenkins
Before you can use emailext in the pipeline, ensure the email configuration is set up in Jenkins:
1.	Install the Email Extension Plugin if not already installed. Go to Manage Jenkins > Manage Plugins and search for Email Extension Plugin to install it.
2.	Configure SMTP Server:
o	Go to Manage Jenkins > Configure System.
o	Scroll down to E-mail Notification and fill in the SMTP server settings (e.g., Gmail, SMTP server, authentication credentials).
Step 5: Testing the Setup
1.	Commit and Push Changes: Ensure that your Jenkinsfile (with the email configuration) is pushed to your Git repository.
2.	Run the Pipeline: Trigger the Jenkins pipeline manually or on code changes.
3.	Check Email Notifications: Based on the success or failure of the build, you should receive email notifications with the subject and body as defined in your Jenkinsfile.
*/