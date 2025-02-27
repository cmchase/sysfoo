pipeline {
  agent none
  stages {
    stage('build') {
      agent {
        docker {
          image 'maven:3.6.3-jdk-11-slim'
        }

      }
      steps {
        echo 'running build'
        sh 'mvn compile'
        echo 'Another print message'
      }
    }

    stage('test') {
      agent {
        docker {
          image 'maven:3.6.3-jdk-11-slim'
        }

      }
      steps {
        echo 'running test'
        sh 'mvn clean test'
      }
    }

    stage('package') {
      agent {
        docker {
          image 'maven:3.6.3-jdk-11-slim'
        }

      }
      steps {
        echo 'running package'
        sh 'mvn package -DskipTests'
        archiveArtifacts 'target/*.war'
      }
    }

    stage('DockerBnP') {
      when {
        branch 'master'
      }
      steps {
        script {
          docker.withRegistry("https://index.docker.io/v1/", "dockerlogin") {
            def dockerImage = docker.build("cmchase/sysfoo:v${env.BUILD_ID}", "./")
            dockerImage.push()
            dockerImage.push("latest")
          }
        }
      }
    }

    stage('Deploy to Dev') {
      when {
        branch 'master'
      }
      agent any
      steps {
        echo 'Deploying to Dev Env'
        sh 'docker-compose up -d'
      }
    }

  }
  tools {
    maven 'Maven 3.6.3'
  }
  post {
    always {
      echo 'This pipeline is completed..'
    }

  }
}