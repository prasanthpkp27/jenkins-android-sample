pipeline {
    agent {
        label "master"
    }

    options {
        buildDiscarder(logRotator(numToKeepStr: '30', daysToKeepStr: '7'))
        timestamps()
    }

    stages {

        

        stage('Build Develop APK') {

            when {
                branch 'master'
            }
            steps {
                withCredentials([string(credentialsId: 'BETA_SECRET_KEY', variable: 'SECRET_KEY')]) {
                    sh './gradlew clean assembleDevDebug'
                }
            }
            post {
                failure {
                    echo "Build Develop APK Failure!"
                }
                success {
                    echo "Build Develop APK Success!"
                }
            }
        }
        stage('Upload') {
            steps {
                archiveArtifacts(artifacts: 'app/build/outputs/apk/**/*.apk', fingerprint: true, onlyIfSuccessful: true)
            }
            post {
                failure {
                    echo "Archive Failure!"
                }
                success {
                    echo "Archive Success!"
                }
            }
        }

        stage('Report') {
            steps {
                echo getChangeString()
            }
        }
    }
}

@NonCPS
def getChangeString() {
    MAX_MSG_LEN = 100
    def changeString = ""

    echo "Gathering SCM Changes..."
    def changeLogSets = currentBuild.changeSets
    for (int i = 0; i < changeLogSets.size(); i++) {
        def entries = changeLogSets[i].items
        for (int j = 0; j < entries.length; j++) {
            def entry = entries[j]
            truncated_msg = entry.msg.take(MAX_MSG_LEN)
            changeString += "[${entry.author}] ${truncated_msg}\n"
        }
    }

    if (!changeString) {
        changeString = " - No Changes -"
    }
    return changeString
}
