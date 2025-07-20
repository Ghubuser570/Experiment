// Jenkinsfile
// This pipeline defines the steps for your Python CI/CD process.
pipeline {
    // This tells Jenkins to run the steps on any available machine (your computer).
    agent any

    // Define the stages (steps) of your automated process.
    stages {
        // Stage 1: Get the latest code from your online storage (GitHub).
        stage('Checkout Code') {
            steps {
                // This command tells Jenkins to pull your project files.
                // Replace 'github-username-pat' with your actual credential ID in Jenkins.
                // Replace the URL with your actual GitHub repository URL.
                git branch: 'main', credentialsId: 'Ghubuser570_Pass', url: 'https://github.com/Ghubuser570/Experiment'
            }
        }

        // Stage 2: Set up a clean Python environment and get all the tools (libraries).
        stage('Prepare Python Environment') {
            steps {
                script {
                    // Create a new, isolated virtual environment named 'venv' in the project folder.
                    // This ensures all tools are installed fresh and don't interfere with other projects.
                    bat 'python -m venv venv'
                    echo "Virtual environment created."

                    // Activate the virtual environment. This makes sure any 'pip' or 'python'
                    // commands use the tools inside this 'venv' box.
                    // Then, install all libraries listed in your 'requirements.txt' file.
                    // This ensures 'pytest' (and any other tools you need) are installed every time.
                    bat 'venv\\Scripts\\activate && pip install -r requirements.txt'
                    echo "All necessary tools (libraries) installed."
                }
            }
        }

        // Stage 3: Run your checks (tests) and save the results.
        stage('Run Tests and Generate Report') {
            steps {
                script {
                    echo "Running tests using pytest..."
                    // Activate the virtual environment.
                    // Then, run pytest.
                    // The '> test_report.txt 2>&1' part is crucial:
                    //   '> test_report.txt' sends the normal output of pytest into a file named 'test_report.txt'.
                    //   '2>&1' sends any error messages (which pytest might also print) into the same file.
                    bat 'venv\\Scripts\\activate && python -m pytest > test_report.txt 2>&1'
                    echo "Test report generated in test_report.txt."
                }
            }
        }

        // Stage 4: Add the new report file to your project and send it back to online storage.
        stage('Publish Test Report') {
            steps {
                script {
                    echo "Adding test report to version control..."
                    // Tell Git (the tool that manages your code changes) to include the new 'test_report.txt' file.
                    bat 'git add test_report.txt'

                    echo "Committing test report..."
                    // Create a "snapshot" of your project, including the new report.
                    // The '--allow-empty-message' is sometimes useful if there are no other code changes.
                    bat 'git commit -m "Automated: Latest test report" --allow-empty-message'

                    echo "Pushing test report to GitHub..."
                    // Send your updated project (with the new report) back to your online storage.
                    // This uses the same credential Jenkins uses to get the code.
                    bat 'git push origin main'
                    echo "Test report published to GitHub."
                }
            }
        }
    }

    // Post-build actions: These steps run after all stages are complete.
    post {
        always {
            echo 'Post-build actions always executed.'
            // Clean up the Jenkins workspace after the build to keep things tidy.
            cleanWs()
        }
        success {
            echo 'Pipeline finished successfully!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}
