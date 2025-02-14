pipeline {
    agent { label 'slave11' }

    environment {
        WORKSPACE_DIR = '/home/ubuntu/test1-appp'
        VENV_DIR = '/home/ubuntu/vvv1'
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
                        pip install pyaudio
                    """
                }
            }
        }

        stage('Configure Nginx') {
            steps {
                script {
                    echo 'Configuring Nginx to proxy to Streamlit...'
                    sh """#!/bin/bash
                        set -e
                        # Install Nginx if necessary
                        sudo apt update
                        sudo apt install -y nginx

                        # Streamlit config setup
                        mkdir -p ~/.streamlit
                        echo "[server]" | tee ~/.streamlit/config.toml
                        echo "headless = true" | tee -a ~/.streamlit/config.toml
                        echo "enableCORS = false" | tee -a ~/.streamlit/config.toml
                        echo "address = '0.0.0.0'" | tee -a ~/.streamlit/config.toml
                        echo "port = 8501" | tee -a ~/.streamlit/config.toml

                        # Create a new Nginx configuration file
                        sudo tee /etc/nginx/sites-available/streamlit > /dev/null <<EOL
                        server {
                            listen 80;
                            server_name localhost;

                            location / {
                                proxy_pass http://0.0.0.0:8501;
                                proxy_set_header Host \$host;
                                proxy_set_header X-Real-IP \$remote_addr;
                                proxy_set_header X-Forwarded-For \$proxy_add_x_forwarded_for;
                                proxy_set_header X-Forwarded-Proto \$scheme;
                            }
                        }
                        EOL

                        # Enable the configuration
                        sudo ln -sf /etc/nginx/sites-available/streamlit /etc/nginx/sites-enabled/
                        sudo rm -f /etc/nginx/sites-enabled/default

                        # Test Nginx configuration and restart the service
                        sudo nginx -t
                        sudo systemctl restart nginx
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
            echo 'Pipeline failed!'
        }
    }
}
