pipeline {
    agent any

    tools {
        maven 'Maven3'
    }

    environment {
        PATH = "${JAVA_HOME}/bin:${PATH}"
    }

    stages {
        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Build & Deploy') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'github-packages-cred',
                    usernameVariable: 'GH_USER',
                    passwordVariable: 'GH_TOKEN'
                )]) {
                    configFileProvider([configFile(fileId: 'maven-github-settings', variable: 'MAVEN_SETTINGS')]) {
                        sh '''
                            set +x

                            MVN_UNIX_PATH="$(cygpath -u "$MAVEN_HOME")/bin/mvn"
                            SETTINGS_UNIX_PATH="$(cygpath -u "$MAVEN_SETTINGS")"

                            "$MVN_UNIX_PATH" -s "$SETTINGS_UNIX_PATH" -B clean package
                            "$MVN_UNIX_PATH" -s "$SETTINGS_UNIX_PATH" -B deploy
                        '''
                    }
                }
            }
        }
    }

    post {
        success {
            echo '✅ Build and deployment to GitHub Packages completed successfully.'
        }
        failure {
            echo '❌ Pipeline failed. Check the console output for details.'
        }
    }
}
