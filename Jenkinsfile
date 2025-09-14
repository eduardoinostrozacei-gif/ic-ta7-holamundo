pipeline {
  agent any
  options { skipDefaultCheckout(true) }

  tools { 
    jdk   'JDK-21'
    maven 'Maven-3.9.11'
  }

  triggers { cron('*/5 * * * *') }

  environment {
    // >>> PON AQUÍ TU RUTA ABSOLUTA EXACTA (entre comillas '...' y con \\) <<<
    PROJECT_DIR = 'C:\\Users\\eduar\\Documents\\IPLACEX\\Bimestre 8\\INTEGRACIÓN CONTINUA\\Semana 7\\ta7\\holamundo'
    DEPLOY_DIR  = 'C:\\deploy\\holamundo'
  }

  stages {
    stage('Checkout desde GitHub') {
      steps { checkout scm }        // evidencia de "descargar código desde GitHub"
    }

    stage('Sanity check de ruta') {
      steps {
        bat 'echo PROYECTO: "%PROJECT_DIR%" & dir "%PROJECT_DIR%"'
        bat 'if not exist "%PROJECT_DIR%\\pom.xml" (echo ERROR: No se encontro pom.xml en "%PROJECT_DIR%" & exit /b 1)'
        bat 'if not exist "%DEPLOY_DIR%" mkdir "%DEPLOY_DIR%"'
      }
    }

    stage('Build (ruta local)') {
      steps {
        bat 'cd /D "%PROJECT_DIR%" && mvn -v'
        bat 'cd /D "%PROJECT_DIR%" && mvn -B clean package'
      }
    }

    stage('Deploy a local') {
      steps {
        // usa comodín por si cambia version del jar
        bat 'copy /Y "%PROJECT_DIR%\\target\\holamundo-*.jar" "%DEPLOY_DIR%\\holamundo.jar"'
        bat 'cd /D "%DEPLOY_DIR%" && (echo Ejecutando... & java -jar holamundo.jar > salida.txt 2>&1) & type salida.txt'
      }
    }
  }

  post {
    success { archiveArtifacts artifacts: '**/target/*.jar', fingerprint: true }
    always  { bat 'if exist "%DEPLOY_DIR%\\salida.txt" type "%DEPLOY_DIR%\\salida.txt"' }
  }
}

