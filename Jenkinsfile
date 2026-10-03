pipeline {
    agent any
    
    environment {
        JAVA_HOME = "/usr/lib/jvm/java-21-openjdk-amd64"
        M2_HOME = "/usr/share/maven"
        PATH = "${JAVA_HOME}/bin:${M2_HOME}/bin:${env.PATH}"
        SONAR_HOST_URL = "http://192.168.33.10:9000"
    }
    
    stages {
        
        // ========== 1. GIT ==========
        stage('Git Checkout') {
            steps {
                echo ">>> [1/6] Récupération du code depuis GitHub..."
                git branch: 'main',
                    url: 'https://github.com/Abassichedly/DevOps-AppGestionDesProjet.git'
            }
        }
        
        // ========== 2. COMPILE ==========
        stage('Compile') {
            steps {
                dir('backend') {
                    echo ">>> [2/6] Compilation avec Maven..."
                    sh 'mvn clean compile'
                }
            }
        }
        
        // ========== 3. TEST ==========
        stage('Test') {
            steps {
                dir('backend') {
                    echo ">>> [3/6] Exécution des tests unitaires..."
                    sh 'mvn test'
                }
            }
        }
        
        // ========== 4. SONARQUBE ==========
        stage('SonarQube Analysis') {
            steps {
                dir('backend') {
                    echo ">>> [4/6] Analyse SonarQube..."
                    withSonarQubeEnv('sonarqube-server') {
                        sh 'mvn sonar:sonar'
                    }
                }
            }
        }
        
        // ========== 5. QUALITY GATE ==========
        stage('Quality Gate') {
            steps {
                echo ">>> [5/6] Attente du Quality Gate..."
                timeout(time: 10, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }
        
        // ========== 6. DOCKER BUILD ==========
        stage('Docker Build') {
            steps {
                echo ">>> [6/6] Construction des images Docker..."
                
                dir('backend') {
                    sh 'docker build -t devops-backend:1.0.0 .'
                }
                
                dir('frontend') {
                    sh 'docker build -t devops-frontend:1.0.0 .'
                }
                
                echo "✅ Images construites : devops-backend:1.0.0 et devops-frontend:1.0.0"
            }
        }
    }
    
    post {
        success {
            echo "✅ Pipeline réussi — Quality Gate PASSED"
        }
        failure {
            echo "❌ Pipeline échoué — Vérifier SonarQube ou le code"
        }
    }
}
