#!groovy

pipeline {
    agent any
    tools {
        jdk 'OpenJDK 11'
        maven 'Maven 3.8.5'
    }

    stages {
        stage('Build') {
            steps {
                echo 'Building..'
                sh 'mvn --version'
                script {
                    def pom = readMavenPom file: 'pom.xml'
                    sh "mvn -B gitflow:release -Drevision=${pom.version.replaceAll("-SNAPSHOT","")}"
                    GIT_TAG = getArtefactVersionFromLastCommitTag()
                }
                echo("GIT_TAG_SELECTOR=${GIT_TAG}")
            }
        }
    }

    post{
        always{
            slackSend channel: 'general' , message: 'please check the pipeline status'
        }
    }
}
def getArtefactVersionFromLastCommitTag() {
    version = sh script: "git describe --abbrev=0 | sed -E 's/^v(.*)/\\1/g' | tr -d '\\n'", returnStdout: true
    return version
}
