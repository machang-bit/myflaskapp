pipeline {
    agent any
    
    environment {
        PYTHON_PATH = 'D:\\Python315\\python.exe'
    }
    
    stages {
        stage('Verify Python Path') {
            steps {
                bat """
                    @echo off
                    echo "=== 验证Python315路径及版本 ==="
                    ${PYTHON_PATH} --version
                    echo "Python路径验证通过！"
                """
            }
        }
        stage('Checkout') {
            steps {
                git url: 'https://github.com/machang-bit/myflaskapp.git', branch: 'main'
            }
        }
        stage('Fix pip') {
            steps {
                bat """
                    @echo off
                    echo "=== 修复Python315的pip环境 ==="
                    ${PYTHON_PATH} -m ensurepip --upgrade  
                    ${PYTHON_PATH} -m pip --version       
                """
            }
        }
        stage('Install Dependencies') {
            steps {
                bat """
                    @echo off
                    ${PYTHON_PATH} -m pip install --upgrade pip
                    ${PYTHON_PATH} -m pip install -r requirements.txt
                """
            }
        }
        stage('Lint') {
            steps {
                bat "${PYTHON_PATH} -m pip install flake8 && ${PYTHON_PATH} -m flake8 app.py tests/"
            }
        }
        stage('Test') {
            steps {
                bat """
                    ${PYTHON_PATH} -m pip install pytest
                    ${PYTHON_PATH} -m pytest --cov=app tests/ --cov-report=html
                """
            }
            post {
                always {
                    publishHTML(target: [
                        allowMissing: false,
                        alwaysLinkToLastBuild: false,
                        keepAll: true,
                        reportDir: 'htmlcov',
                        reportFiles: 'index.html',
                        reportName: 'Coverage Report'
                    ])
                }
            }
        }
        stage('Build') {
            steps {
                bat """
                    ${PYTHON_PATH} -m pip install pyinstaller
                    ${PYTHON_PATH} -m PyInstaller --onefile app.py
                """
            }
        }
        stage('Deploy') {
            steps {
                // 这里注释掉直接启动，防止Jenkins流水线卡住（作业大坑！）
                echo 'Deploying application... 实验仅打包，不常驻启动Flask避免流水线阻塞'
            }
        }
    }
    post {
        success { echo 'CI/CD pipeline completed successfully!' }
        failure { echo 'CI/CD pipeline failed!' }
    }
}
