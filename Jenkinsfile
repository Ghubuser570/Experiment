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
                // Use the PAT credential for cloning/fetching.
                // Updated credentialsId and URL based on your precise details.
                git branch: 'main', credentialsId: 'Ghubuser570_PAT', url: 'https://github.com/Ghubuser570/Experiment.git'
            }
        }

        // Stage 2: Set up a clean Python environment and get all the tools (libraries).
        stage('Prepare Python Environment') {
            steps {
                script {
                    // Use the full path to python.exe to create the virtual environment.
                    bat '"C:\\Users\\75\\AppData\\Local\\Programs\\Python\\Python310\\python.exe" -m venv venv'
                    echo "Virtual environment created."

                    // Activate the virtual environment. This makes sure any 'pip' or 'python'
                    // commands use the tools inside this 'venv' box.
                    // Then, install all libraries listed in your 'requirements.txt' file.
                    // This ensures 'pytest' (and any other tools you need) are installed every time.
                    bat 'venv\\Scripts\\activate && "C:\\Users\\75\\AppData\\Local\\Programs\\Python\\Python310\\python.exe" -m pip install -r requirements.txt'
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
                    // Then, run pytest using the full python.exe path.
                    // The '> test_report.txt 2>&1' part is crucial:
                    //   '> test_report.txt' sends the normal output of pytest into a file named 'test_report.txt'.
                    //   '2>&1' sends any error messages (which pytest might also print) into the same file.
                    bat 'venv\\Scripts\\activate && "C:\\Users\\75\\AppData\\Local\\Programs\\Python\\Python310\\python.exe" -m pytest > test_report.txt 2>&1'
                    echo "Test report generated in test_report.txt."
                }
            }
        }

        // Stage 4: Add the new report file to your project and send it back to online storage.
        stage('Publish Test Report') {
            steps {
                script {
                    echo "Adding test report to version control..."
                    // Tell Git who is making this automated commit.
                    bat 'git config user.email "jenkins@example.com"' // You can use any valid email
                    bat 'git config user.name "Jenkins Automated Build"' // You can use any name you like
                    
                    // Tell Git (the tool that manages your code changes) to include the new 'test_report.txt' file.
                    bat 'git add test_report.txt'

                    echo "Committing test report..."
                    // Create a "snapshot" of your project, including the new report.
                    // The '--allow-empty-message' is sometimes useful if there are no other code changes.
                    bat 'git commit -m "Automated: Latest test report" --allow-empty-message'

                    echo "Pushing test report to GitHub..."
                    // Use withCredentials to securely provide the PAT for the git push.
                    // 'Ghubuser570_pat' is the ID of your Secret Text credential in Jenkins.
                    withCredentials([usernamePassword(credentialsId: 'Ghubuser570_PAT', usernameVariable: 'GIT_USERNAME', passwordVariable: 'GIT_PASSWORD')]) {
                        // Create a temporary batch script that simply echoes the PAT.
                        bat 'echo %GIT_PASSWORD% > git_askpass_script.bat'
                        bat 'echo exit 0 >> git_askpass_script.bat' // Ensure the script exits cleanly

                        // Set GIT_ASKPASS environment variable to point to our temporary script.
                        // Set GIT_USERNAME to your GitHub username (can be anything for PAT, but good practice).
                        // Then, perform the push.
                        bat 'set GIT_ASKPASS=%CD%\\git_askpass_script.bat && set GIT_USERNAME=Ghubuser570 && git push origin main'

                        // Clean up the temporary script immediately after the push.
                        bat 'del git_askpass_script.bat'
                    }
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
