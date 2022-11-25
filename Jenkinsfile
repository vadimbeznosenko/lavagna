pipeline {
    agent {label 'agent_win'}

options { disableConcurrentBuilds() }
    stages {
        stage ('Build on Windows'){
            agent {label 'agent_win'}
            steps {

            withMaven(
            jdk: 'openlogic-openjdk-8u352-b08-windows',
            maven: 'apache-maven-3.5.0-win',
            options: [junitPublisher(disabled: true),
            openTasksPublisher(disabled: true)]){
                
                bat "mvn clean"
                bat "mvn package"

                zip zipFile: "${BUILD_DISPLAY_NAME}_win:v${BUILD_NUMBER}.zip",
                glob : "${WORKSPACE}\\target\\lavagna-jetty-console.war",
                overwrite : true

                stash includes: "${BUILD_DISPLAY_NAME}_win:v${BUILD_NUMBER}.zip",
                name: 'lavaga'
            }
            }
        post {
        always {
            cleanWs()
        }
        }
        }
        stage ('Build on Linux') {
            agent {label 'agent_lin'}
            steps {

            withMaven(
            jdk: 'java/jdk-8u202-linux',
            maven: 'apache-maven-3.5.0-lin')
{

                sh 'mvn package'

                sh "ls -la ${WORKSPACE}/target/"

                zip zipFile: "${WORKSPACE}/build/${BUILD_DISPLAY_NAME}_lin:v${BUILD_NUMBER}.zip",
                glob : "${WORKSPACE}/target/lavagna-jetty-console.war"
}

            }
        }
        stage ('Deploy artifact') {

            agent {label 'agent_lin'}

            steps {

            dir("${WORKSPACE}/build/") {
            unstash "${JOB_NAME}"
            }

            withCredentials([[
            credentialsId: 'artifactory-access-token',
            variable: 'ARTIFACTORY_ACCESS_TOKEN'
           ]]){
            sh "jf rt upload --url http://192.168.31.13:8082/artifactory --access-token $ARTIFACTORY_ACCESS_TOKEN ${WORKSPACE}/build/*.zip SNAPSHOTS/"
            }
            }
        post {
        always {
            cleanWs()
        }
        }
        }

    }
}
