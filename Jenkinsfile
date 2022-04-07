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
                sh "git pull origin main"
                scmSkip(deleteBuild: false, skipPattern:'\\[ci skip\\].*')
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
def getArtefactVersionFromLastCommitTag() {
    version = sh script: "git describe --abbrev=0 | sed -E 's/^v(.*)/\\1/g' | tr -d '\\n'", returnStdout: true
    return version
}
