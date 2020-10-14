#!/usr/bin/env groovy

pipeline {
  environment {
  registry = "vermunoz/todo-frontend"
  registryCredential = "dockerhub"
  }
 agent none

 stages {
   stage('Unit test frontend') {
     agent any

     steps {
         sh "docker build -t frontend-tests -f Frontend/Dockerfile.unit-test ./Frontend"
         sh "docker run frontend-tests"
     }
   }
   stage('Build frontend') {
     agent any
     when {
       branch 'main'
     }
     steps {
         sh "docker pull vermunoz/ecsfs-frontend:latest"
         sh "docker build -t vermunoz/todo-frontend:${GIT_COMMIT} -f Frontend/Dockerfile ./Frontend"
         sh "docker push vermunoz/todo-frontend:${GIT_COMMIT}"
     }
   }
   stage('Unit test backend') {
     agent any

     steps {
         sh "docker build -t backend-tests -f Backend/Dockerfile.unit-test ./Backend"
     }
   }
   stage('Build backend') {
     agent any
     when {
       branch 'main'
     }
     steps {
         sh "docker build -t vermunoz/todo-backend:${GIT_COMMIT} -f Backend/Dockerfile ./Backend"
         sh "docker push vermunoz/todo-backend:${GIT_COMMIT}"
     }
   }
   stage('Cleanup tests'){
     agent any
     steps {
       sh "docker rmi -f frontend-tests"
       sh "docker rmi -f backend-tests"
     }
   }
   stage('Cleanup images'){
     agent any
     when {
       branch 'main'
     }
     steps {
       sh "docker rmi -f vermunoz/todo-frontend:${GIT_COMMIT}"
       sh "docker rmi -f vermunoz/todo-backend:${GIT_COMMIT}"
     }
   }
 }
}
