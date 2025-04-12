pipeline {
    agent any

    environment {
        // Set up environment variables for Ansible
        ANSIBLE_HOST_KEY_CHECKING = "False"   // Disable SSH host key checking
        ANSIBLE_INVENTORY = "/home/jenkins/ansible/inventory"  // Path to the inventory file in the Jenkins container
        EMAIL_RECIPIENTS = "youremail@example.com"  // Replace with your email address
    }

    stages {
        stage('Checkout') {
            steps {
                // Checkout the Git repository containing the Jenkinsfile and Ansible playbook
                git 'https://github.com/your-repo/your-project.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                script {
                    // Install Ansible if it's not already installed in the Jenkins container
                    // Run this step if your Jenkins container doesn't have Ansible pre-installed
                    sh 'apt-get update && apt-get install -y ansible'
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
                    // Run the Ansible playbook using the inventory file
                    sh 'ansible-playbook /home/jenkins/ansible/deploy.yml -i $ANSIBLE_INVENTORY'
                }
            }
        }
    }

    post {
        success {
            // Send email on successful build
            emailext (
                subject: "SUCCESS: Build ${currentBuild.fullDisplayName}",
                body: "The Ansible playbook executed successfully and deployment was completed.\n\nPlease visit ${env.BUILD_URL} for more details.",
                to: "${EMAIL_RECIPIENTS}"
            )
            echo 'Playbook executed successfully, deployment completed.'
        }
        failure {
            // Send email on failure
            emailext (
                subject: "FAILURE: Build ${currentBuild.fullDisplayName}",
                body: "An error occurred during playbook execution. The deployment failed.\n\nPlease visit ${env.BUILD_URL} to review the logs and resolve the issues.",
                to: "${EMAIL_RECIPIENTS}"
            )
            echo 'An error occurred during playbook execution, deployment failed.'
        }
        unstable {
            // Send email for unstable builds (e.g., test failures)
            emailext (
                subject: "UNSTABLE: Build ${currentBuild.fullDisplayName}",
                body: "The build ${currentBuild.fullDisplayName} was unstable.\n\nPlease visit ${env.BUILD_URL} to review the logs.",
                to: "${EMAIL_RECIPIENTS}"
            )
            echo 'The build was unstable, review the logs for more information.'
        }
        always {
            // Always send an email after the build (can be used for additional cleanup tasks or notifications)
            echo 'Sending final email notification...'
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

*/