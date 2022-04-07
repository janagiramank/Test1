#!groovy

pipeline {
    agent any
    tools {
        jdk 'OpenJDK 11'
        maven 'Maven 3.8.5'
    }

    stages {
        stage('Checkout') {
            steps {
                sh "git log -1"
                scmSkip(deleteBuild: false, skipPattern:'\\[ci skip\\].*')
                sh 'sleep 3'
                echo 'checkout completed'
            }
        }
        stage('Build') {
            steps {
                echo 'Building..'
                sh 'mvn --version'
                script {
                    withCredentials([gitUsernamePassword(credentialsId: 'git-up', gitToolName: 'git-tool')]) {
                        sh "git config --global --add user.name janagiramank"
                        sh "git config --global --add user.email k.janagiraman@elsevier.com"
                        def pom = readMavenPom file: 'pom.xml'
                        sh "mvn -B gitflow:release -Drevision=${pom.version.replaceAll("-SNAPSHOT","")}"
                    }
                }
                echo 'build completed'
            }
        }

    }

    post{
        always{
            deleteDir()
            slackSend channel: 'general' , message: 'please check the pipeline status'
        }
    }
}
