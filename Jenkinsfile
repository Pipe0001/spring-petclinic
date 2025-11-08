pipeline {
  agent none

  stages {

    stage('Maven Install') {
      agent {
        docker {
          image 'maven:3.9-eclipse-temurin-25'
          reuseNode true
        }
      }
      steps {
        sh 'mvn clean install -DskipTests'
        stash includes: 'target/*.jar', name: 'jarfile'
      }
    }

    stage('Docker Build') {
      agent any
      steps {
        unstash 'jarfile'
        sh 'docker build -t piper305/spring-petclinic:latest .'
      }
    }

    stage('Docker Push') {
      agent any
      steps {
        withCredentials([usernamePassword(credentialsId: 'dockerHub', passwordVariable: 'dockerHubPassword', usernameVariable: 'dockerHubUser')]) {
          sh "docker login -u ${env.dockerHubUser} -p ${env.dockerHubPassword}"
          sh 'docker push piper305/spring-petclinic:latest'
        }
      }
    }
  }
}
