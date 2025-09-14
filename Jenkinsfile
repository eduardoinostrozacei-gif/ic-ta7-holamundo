pipeline {
    agent any

    tools {
        jdk   'JDK-21'         
        maven 'Maven-3.9.11'  
    }

    triggers {
        // Ejecuta cada 5 minutos
        cron('*/5 * * * *')
    }

    envi
        DEPLOY_DIR = "C:\\\\deploy\\\\holamundo"
    }

    stages {

        stage('Checkout from GitHub') {
            steps {
                checkout([$class: 'GitSCM',
                    branches: [[name: '*/main']],
                    userRemoteConfigs: [[
                        url: 'https://github.com/eduardoinostrozacei-gif/ic-ta7-holamundo.git',
                        credentialsId: 'github-token'
                    ]]
                ])
            }
        }

        stage('Build with Maven') {
            steps {
                bat 'mvn -v'
                bat 'mvn -B clean package'
            }
        }

        stage('Deploy to local') {
            steps {
                // Crear carpeta de despliegue
                bat 'if not exist "%DEPLOY_DIR%" mkdir "%DEPLOY_DIR%"'
                // Copiar jar
                bat 'copy /Y "target\\holamundo-1.0-SNAPSHOT.jar" "%DEPLOY_DIR%\\holamundo.jar"'
                // Ejecutar jar y guardar salida
                bat 'cd /D "%DEPLOY_DIR%" && (echo Ejecutando... & java -jar holamundo.jar > salida.txt 2>&1) & type salida.txt'
            }
        }
    }

    post {
        success {
            archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
        }
        always {
            // Mostrar salida si existe
            bat 'if exist "%DEPLOY_DIR%\\salida.txt" type "%DEPLOY_DIR%\\salida.txt"'
        }
    }
}
