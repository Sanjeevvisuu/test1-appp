pipeline {
    agent { label 'slave11' }

    environment {
        // Define environment variables
        STREAMLIT_CONFIG = "${env.HOME}/.streamlit/config.toml"
        NGINX_CONFIG = "/etc/nginx/nginx.conf"
        APP_REPO = "https://github.com/Sanjeevvisuu/test1-appp.git" // Your repository URL
        APP_DIR = "${env.WORKSPACE}/test1-appp" // Directory where the repo will be cloned
    }

    stages {
        stage('Prepare Environment') {
            steps {
                script {
                    // Create Streamlit config directory and file
                    sh """
                        mkdir -p ${env.HOME}/.streamlit
                        echo '[server]' > ${STREAMLIT_CONFIG}
                        echo 'headless = true' >> ${STREAMLIT_CONFIG}
                        echo 'enableCORS = false' >> ${STREAMLIT_CONFIG}
                        echo 'address = "0.0.0.0"' >> ${STREAMLIT_CONFIG}
                        echo 'port = 8501' >> ${STREAMLIT_CONFIG}
                    """
                }
            }
        }

        stage('Install Dependencies') {
            steps {
                script {
                    // Install system dependencies
                    sh "sudo apt-get update"
                    sh "sudo apt-get install -y portaudio19-dev"

                    // Install Python packages
                    sh """
                        pip install streamlit pandas plotly gTTS SpeechRecognition mysql-connector-python matplotlib pillow pickle-mixin groq num2words pyaudio
                    """
                }
            }
        }

        stage('Clone Application') {
            steps {
                script {
                    // Clone the application repository
                    sh """
                        git clone ${APP_REPO} ${APP_DIR}
                    """
                }
            }
        }

        stage('Configure Nginx') {
            steps {
                script {
                    // Backup existing Nginx configuration
                    sh "sudo cp ${NGINX_CONFIG} ${NGINX_CONFIG}.bak"

                    // Create new Nginx configuration
                    sh """
                        echo 'events {}' | sudo tee ${NGINX_CONFIG}
                        echo 'http {' | sudo tee -a ${NGINX_CONFIG}
                        echo '    include       mime.types;' | sudo tee -a ${NGINX_CONFIG}
                        echo '    default_type  application/octet-stream;' | sudo tee -a ${NGINX_CONFIG}
                        echo '    server {' | sudo tee -a ${NGINX_CONFIG}
                        echo '        listen 80;' | sudo tee -a ${NGINX_CONFIG}
                        echo '        server_name localhost;' | sudo tee -a ${NGINX_CONFIG}
                        echo '        location / {' | sudo tee -a ${NGINX_CONFIG}
                        echo '            proxy_pass http://0.0.0.0:8501;' | sudo tee -a ${NGINX_CONFIG}
                        echo '            proxy_set_header Host \$host;' | sudo tee -a ${NGINX_CONFIG}
                        echo '            proxy_set_header X-Real-IP \$remote_addr;' | sudo tee -a ${NGINX_CONFIG}
                        echo '            proxy_set_header X-Forwarded-For \$proxy_add_x_forwarded_for;' | sudo tee -a ${NGINX_CONFIG}
                        echo '            proxy_set_header X-Forwarded-Proto \$scheme;' | sudo tee -a ${NGINX_CONFIG}
                        echo '            proxy_set_header Upgrade \$http_upgrade;' | sudo tee -a ${NGINX_CONFIG}
                        echo '            proxy_set_header Connection "upgrade";' | sudo tee -a ${NGINX_CONFIG}
                        echo '            proxy_http_version 1.1;' | sudo tee -a ${NGINX_CONFIG}
                        echo '            proxy_set_header Connection keep-alive;' | sudo tee -a ${NGINX_CONFIG}
                        echo '            proxy_read_timeout 1000;' | sudo tee -a ${NGINX_CONFIG}
                        echo '            proxy_send_timeout 1000;' | sudo tee -a ${NGINX_CONFIG}
                        echo '        }' | sudo tee -a ${NGINX_CONFIG}
                        echo '        error_page 500 502 503 504 /50x.html;' | sudo tee -a ${NGINX_CONFIG}
                        echo '        location = /50x.html {' | sudo tee -a ${NGINX_CONFIG}
                        echo '            root html;' | sudo tee -a ${NGINX_CONFIG}
                        echo '        }' | sudo tee -a ${NGINX_CONFIG}
                        echo '    }' | sudo tee -a ${NGINX_CONFIG}
                        echo '}' | sudo tee -a ${NGINX_CONFIG}
                    """

                    // Test Nginx configuration and restart
                    sh "sudo nginx -t"
                    sh "sudo systemctl restart nginx"
                }
            }
        }

        stage('Run Application') {
            steps {
                script {
                    // Run the Streamlit application in the background
                    sh """
                        cd ${APP_DIR}
                        nohup streamlit run final12.py > output.log 2>&1 &
                    """
                }
            }
        }

       
        
    }

    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}
