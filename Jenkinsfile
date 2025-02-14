pipeline {
    agent  { label 'slave11' }

    stages {
        stage('Checkout') {
            steps {
                echo 'Checking out the code...'
                git branch: 'main', url: 'https://github.com/Sanjeevvisuu/test1-appp.git'
            }
        }

        stage('Activate Virtual Environment') {
            steps {
                script {
                    sh '''#!/bin/bash
                    set -e
                    sudo apt update
                    sudo apt install -y python3-venv
                    python3 -m venv vvv
                    source vvv/bin/activate
                    '''
                }
            }
        }

        stage('Install Dependencies') {
            steps {
                script {
                    sh '''#!/bin/bash
                    set -e
                    source vvv/bin/activate
                    pip install streamlit pandas plotly gTTS SpeechRecognition mysql-connector-python matplotlib pillow pickle-mixin groq num2words pyaudio
                    '''
                }
            }
        }

        stage('Configure Nginx') {
            steps {
                script {
                    sh '''#!/bin/bash
                    set -e
                    # Nginx installation if needed
                    sudo apt update
                    sudo apt install -y nginx

                    # Streamlit config setup
                    mkdir -p ~/.streamlit
                    echo "[server]" > ~/.streamlit/config.toml
                    echo "headless = true" >> ~/.streamlit/config.toml
                    echo "enableCORS = false" >> ~/.streamlit/config.toml
                    echo "address = '0.0.0.0'" >> ~/.streamlit/config.toml
                    echo "port = 8501" >> ~/.streamlit/config.toml

                    # Remove existing nginx.conf and create a new one
                    sudo rm /etc/nginx/nginx.conf
                    sudo tee /etc/nginx/nginx.conf > /dev/null <<EOL
                    events {}

                    http {
                        include       mime.types;
                        default_type  application/octet-stream;

                        access_log /var/log/nginx/access.log;  # Access log configuration
                        error_log /var/log/nginx/error.log;    # Error log configuration

                        server {
                            listen 80;
                            server_name localhost;

                            location / {
                                proxy_pass http://0.0.0.0:8501;  # Proxy requests to Streamlit app
                                proxy_set_header Host \$host;
                                proxy_set_header X-Real-IP \$remote_addr;
                                proxy_set_header X-Forwarded-For \$proxy_add_x_forwarded_for;
                                proxy_set_header X-Forwarded-Proto \$scheme;
                                proxy_set_header Upgrade \$http_upgrade;
                                proxy_set_header Connection "upgrade";
                                proxy_http_version 1.1;
                                proxy_set_header Connection keep-alive;
                                proxy_read_timeout 1000;
                                proxy_send_timeout 1000;
                            }

                            error_page 500 502 503 504 /50x.html;
                            location = /50x.html {
                                root html;
                            }
                        }
                    }
                    EOL

                    # Test Nginx configuration and restart service
                    sudo nginx -t
                    sudo systemctl restart nginx
                    '''
                }
            }
        }

        stage('Run Streamlit App') {
            steps {
                script {
                    sh '''#!/bin/bash
                    set -e
                    nohup streamlit run final12.py > output.log 2>&1 &
                    '''
                }
            }
        }
    }

    post {
        always {
            echo 'Cleaning up workspace...'
            // Optionally add cleanup steps like Docker image cleanup
        }
        success {
            echo 'Pipeline succeeded!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}
