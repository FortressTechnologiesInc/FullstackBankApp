pipeline {
    agent any
    
    tools{
        jdk 'jdk'
        nodejs 'nodejs'
        
    }
    
    environment{
        SCANNER_HOME= tool 'scanner'
        
    }
    
    stages {
        stage('Git Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/FortressTechnologiesInc/FullstackBankApp.git'
            }
        }
        
        stage('OWASP FS SCAN') {
            steps {
                dependencyCheck additionalArguments: '--scan ./app/backend --disableYarnAudit --disableNodeAudit', odcInstallation: 'DC'
                    dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }
        
        stage('TRIVY FS SCAN') {
            steps {
                sh "trivy fs ."
            }
        }
        
        stage('SONARQUBE ANALYSIS') {
            steps {
                withSonarQubeEnv('sonar') {
                    sh " $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=Bankapp -Dsonar.projectKey=Bankapp "
                }
            }
        }
        
        
         stage('Install Dependencies') {
    steps {
        dir('/root/appnode/workspace/FullstackBankApp/app') {
            sh 'npm install'
        }
    }
}

        
        stage('Backend') {
    steps {
        dir('/root/appnode/workspace/FullstackBankApp/app/backend/build') {
            sh 'npm install'
        }
    }
}

        
        stage('frontend') {
            steps {
                dir('/root/appnode/workspace/FullstackBankApp/app/frontend/build') {
                    sh "npm install"
                }
            }
        }
        
        stage('Deploy to Conatiner') {
            steps {
                sh "npm run compose:up -d"
            }
        }
    }
}
