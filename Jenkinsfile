pipeline {
  agent any

  options {
    // Evita el checkout automático; haremos uno explícito
    skipDefaultCheckout(true)
  }

  tools {
    jdk   'JDK-21'         // Debe existir en Manage Jenkins > Tools
    maven 'Maven-3.9.11'   // Debe existir en Manage Jenkins > Tools
  }

  // Ejecuta cada 5 minutos
  triggers {
    cron('*/5 * * * *')
  }

  // Ruta Windows para desplegar
  environment {
    DEPLOY_DIR = 'C:\\deploy\\holamundo'
  }

  stages {
    stage('Checkout from GitHub') {
      steps {
        // Toma la misma URL/credencial del job (no pongas URL aquí)
        checkout scm
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
        bat 'if not exist "%DEPLOY_DIR%" mkdir "%DEPLOY_DIR%"'
        bat 'copy /Y "target\\holamundo-1.0-SNAPSHOT.jar" "%DEPLOY_DIR%\\holamundo.jar"'
        bat 'cd /D "%DEPLOY_DIR%" && (echo Ejecutando... & java -jar holamundo.jar > salida.txt 2>&1) & type salida.txt'
      }
    }
  }

  post {
    success {
      archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
    }
    always {
      bat 'if exist "%DEPLOY_DIR%\\salida.txt" type "%DEPLOY_DIR%\\salida.txt"'
    }
  }
}

