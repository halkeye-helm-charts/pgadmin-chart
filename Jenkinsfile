pipeline {
    agent any

    options {
        timeout(time: 10, unit: 'MINUTES')
        ansiColor('xterm')
    }
    stages {
        stage('Checkout halkeye/helm-charts') {
          steps {
            dir('helm-charts') {
              sh '
              git changelog: false, credentialsId: 'github-halkeye', poll: false, url: 'git@github.com:halkeye/helm-charts.git'
            }
          }
        }

        stage('Checkout chart') {
          steps {
            dir('chart') {
              checkout scm
            }
          }
        }

        stage('Build') {
            steps {
                dir('chart') {
                  sh 'help lint .'
                  sh 'helm package .'
                  sh 'mv *.tgz ../helm-charts/pgadmin/'
                }
                dir('helm-charts') {
                  sh 'git config --global user.email "jenkins@gavinmogan.com"'
                  sh 'git config --global user.name "Jenkins"'
                  sh 'git add .'
                  sh 'git commit -m "Adding package"
                }
            }
        }

        stage('Deploy') {
            when {
                branch 'master'
            }
            environment {
                GITHUB = credentials('credentialsId')
            }
            steps {
                sh 'echo ${GITHUB_USR} pushing '
                sh 'git config --global user.email "jenkins@gavinmogan.com"'
                sh 'git config --global user.name "Jenkins"'
                sh 'git config --global push.default simple'
                sh 'git push https://${GITHUB_USR}:${GITHUB_PSW}@github.com/halkeye/helm-charts.git'
              }
            }
        }
    }
}
