pipeline {
    agent { label 'slave11' }
    environment {
        WORKSPACE_DIR = '/home/ubuntu/test1-appp'
        VENV_DIR = '/home/ubuntu/vvv1'
        STREAMLIT_LOG = '/home/ubuntu/test1-appp/output.log'  // Path to capture logs
    }

    stages {
        stage('Checkout') {
            steps {
                echo 'Checking out the code from Git repository...'
                sh """
                    cd /home/ubuntu
                    rm -rf ${WORKSPACE_DIR}  # Clean up any existing repository
                    git clone https://github.com/Sanjeevvisuu/test1-appp.git ${WORKSPACE_DIR}
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
                        sudo apt install -y python3-venv
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
                    sh """#!/bin/bash
                        set -e
                        source ${VENV_DIR}/bin/activate
                        cd ${WORKSPACE_DIR}
                        nohup streamlit run final12.py > output.log 2>&1 &
                        
                    """
                }
            }
        }

        
    }

    post {
        always {
            echo 'Cleaning up workspace...'
            cleanWs()  // Clean the workspace after the pipeline finishes
        }
        success {
            echo 'Pipeline succeeded!'
        }
        failure {
            echo 'Pipeline failed! Check the logs for errors.'
        }
    }
}
