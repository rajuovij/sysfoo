pipeline {
  agent none
  stages {
    stage('Build') {
      agent {
        docker {
          image 'maven:3.6.3-jdk-11-slim'
        }

      }
      steps {
        sh 'mvn compile'
      }
    }

    stage('Test') {
      agent {
        docker {
          image 'maven:3.6.3-jdk-11-slim'
        }

      }
      steps {
        sh 'mvn clean test'
      }
    }

    stage('Package') {
      agent {
        docker {
          image 'maven:3.6.3-jdk-11-slim'
        }

      }
      when {
        branch 'master'
      }
      steps {
        sh 'mvn package -DskipTest'
        archiveArtifacts 'target/*.war'
      }
    }

    stage('B&P') {
      agent any
      when {
        branch 'master'
      }
      steps {
        script {
          docker.withRegistry('https://index.docker.io/v1/', 'dockerlogin') {

            def dockerImage = docker.build("rajuovij/sysfoo:v${env.BUILD_ID}", "./")

            dockerImage.push()

            dockerImage.push("latest")
            dockerImage.push("dev")

          }
        }

      }
    }

    stage('Deploy to Dev') {
      agent any
      steps {
        sh 'docker-compose up -d'
      }
    }

  }
  tools {
    maven 'Maven 3.6.3'
  }
}