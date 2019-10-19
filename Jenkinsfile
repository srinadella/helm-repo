pipeline {
  environment {
    registry = "cnadella/docker-test"
    registryCredential = 'DockerHub'
    dockerImage = ''
  }
  agent any
  stages {
    stage ("Sleep to Make sure Helm Repo Can be refreshed in") {
      steps{
      echo 'Waiting 2 minutes for deployment to complete prior starting packaging'
      // sleep 120 // seconds
      }
    }
    stage('Cloning Git') {
      steps {
        checkout scm
      }
    }
    stage('Update Repo Index and Commit to Github') {
      steps{
        script {
          sh "cd /var/lib/jenkins/workspace/helm-repo"
          sh "ls -al"
          sh "/usr/local/bin/helm repo index ."
          sh "git remote show origin"
          sh "git config --global user.name 'Sri Nadella'"
          sh "git config --global user.email cnadella@gmail.com"
          sh "git status"
          sh "git remote set-url origin https://${env.GIT_USER_NAME}:${env.GITPASS}@github.com/${env.GIT_USER_NAME}/helm-repo.git"
          sh "git add ."
          sh "git commit -m 'Commiting updated Package and Index from Jenkins'"
          sh "git push origin HEAD:master"
        }
      }
    }

    stage ("Waiting to Make sure Helm Repo Can be pulled in") {
      steps{
        echo 'Waiting 1 minute for deployment to complete prior starting again'
        echo "$PWD"
      }
    }
    stage('Update Local Helm Repo') {
      steps{
        script {
          sh "/usr/local/bin/helm repo update"
        }
      }
    }
    stage('List Local Helm Repo') {
      steps{
        script {
          sh "/usr/local/bin/helm search helm-charts"
        }
      }
    }
    stage('Install Heml Chart') {
      steps{
        sh "/usr/local/bin/helm update --name mynode helm-charts/mynode"
      }
    }
  }
}