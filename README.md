pipeline {
  agent any
  stages {
    stage('Build') {
      steps {
        sh 'mvn -B -DskipTests package'   // on Windows use bat
      }
    }
    stage('Test') {
      steps {
        sh 'mvn test'
      }
    }
  }
  post {
    always { junit '**/target/surefire-reports/*.xml' }
  }
}

2

pipeline {
  agent any

  stages {
    stage('Clone') {
      steps {
        // clone repo
        git url: 'https://github.com/keerthikaeaday/bbb', branch: 'main'
      }
    }

    stage('Build') {
      steps {
        // simple build step (for windows Jenkins use bat)
        bat 'echo Running build... & dir'
      }
    }
  }

  post {
    success { echo 'Two-stage pipeline finished SUCCESS' }
    failure { echo 'Two-stage pipeline FAILED' }
  }
}

3

E Keerthika <keerthikaeaday93@gmail.com>
9:17 AM (22 minutes ago)
to me

node {
    stage('Start') {
        echo "Scripted pipeline: Start"
    }

    stage('Build') {
        echo "Scripted pipeline: Build"
        bat """
        echo Compiling...
        rem short wait - ping localhost 2 times (works as sleep)
        ping -n 2 127.0.0.1 >nul
        """
    }

    stage('Test') {
        echo "Scripted pipeline: Test"
        bat """
        echo Running tests...
        ping -n 2 127.0.0.1 >nul
        """
    }

    stage('Deploy') {
        echo "Scripted pipeline: Deploy"
        bat """
        echo Deploying...
        ping -n 2 127.0.0.1 >nul
        """
    }
}
sc
pipeline {
  agent any

  stages {
    stage('Clone') {
      steps {
        git url: 'https://github.com/keerthikaeaday/bbb', branch: 'main'
      }
    }

    stage('Build web project') {
      steps {
        // change directory to web project then run mvn
        bat 'cd maven-webproject && mvn -B -DskipTests package'
      }
    }

    stage('Test (skip)') {
      steps {
        echo 'Skipping tests for now'
      }
    }
  }

  post {
    always {
      archiveArtifacts artifacts: 'maven-webproject/target/*.war, maven-webproject/target/*.jar', allowEmptyArchive: true
    }
    success {
      echo 'Build completed SUCCESS'
    }
    failure {
      echo 'Build FAILED — check console output'
    }
  }
}
