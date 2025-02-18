pipeline {
    agent { label 'slave11' }
    environment {
        WORKSPACE_DIR = '/home/ubuntu/test1-appp'
        VENV_DIR = '/home/ubuntu/vvv1'
        STREAMLIT_LOG = '/home/ubuntu/test1-appp/output.log'
        STREAMLIT_APP = 'final12.py' 
        
    }

    stages {
        stage('Checkout') {
            steps {
                echo 'Checking out the code from Git repository...'
                sh """
                    cd /home/ubuntu
                    rm -rf ${WORKSPACE_DIR}  # Clean up any existing repository
                    git clone https://github.com/Sanjeevvisuu/test1-appp.git
                """
            }
        }

        stage('Activate Virtual Environment') {
            steps {
                script {
                    echo 'Setting up Python virtual environment...'
                    sh """#!/bin/bash
                        set -e
                        sudo apt update
                        sudo apt install -y python3-venv build-essential  # Install build-essential for gcc
                        python3 -m venv ${VENV_DIR}
                        source ${VENV_DIR}/bin/activate
                    """
                }
            }
        }

        stage('Install Dependencies') {
            steps {
                script {
                    echo 'Installing Python dependencies...'
                    sh """#!/bin/bash
                        set -e
                        source ${VENV_DIR}/bin/activate
                        sudo apt-get update
                        sudo apt-get install -y python3-dev portaudio19-dev
                        pip install streamlit pandas plotly gTTS SpeechRecognition mysql-connector-python matplotlib pillow pickle-mixin groq num2words
                        sudo apt-get install -y portaudio19-dev
                        pip install pyaudio
                    """
                }
            }
        }

        stage('Run Streamlit App') {
            steps {
                script {
                    echo 'Running the Streamlit app in the background...'
                    sh '''#!/bin/bash
                        source ${VENV_DIR}/bin/activate
                        nohup streamlit run ${WORKSPACE_DIR}/${STREAMLIT_APP} > ${STREAMLIT_LOG} 2>&1 &
                        sleep 5
                        ps aux | grep streamlit
                        sudo systemctl restart streamlit-app.service

                    '''
                }
            }
        }

        stage('Test Streamlit App') {
            steps {
                script {
                    echo 'Testing if Streamlit app is running...'
                    def status = sh(script: 'curl --max-time 10 --silent http://localhost:8501 || echo "failed"', returnStatus: true)
                    if (status != 0) {
                        error 'Streamlit app did not start successfully.'
                    }
                }
            }
        }
    }
       post {
        always {
            echo 'Cleaning up workspace...' 
        }
        success {
            echo 'Pipeline succeeded!'
            
            emailext(
                subject: "Jenkins Pipeline Succeeded: SpacECE-India-Foundation portal - Build #${BUILD_NUMBER}",
                body: """ The Jenkins pipeline for the SpacECE-India-Foundation portal has succeeded.
                 
                    Please check the Jenkins console output for details.""",
                to: "sanjeevchitra23092002@gmail.com"
            )
        }
        failure {
            echo 'Pipeline failed!'
            
            emailext(
                subject: "Jenkins Pipeline Failed: SpacECE-India-Foundation portal - Build #${BUILD_NUMBER}",
                body: """The Jenkins pipeline for the SpacECE-India-Foundation portal has failed.
                
                    Please check the Jenkins console output for details.""",
                to: "sanjeevchitra23092002@gmail.com"
            )
        }
    }
}
