pipeline {
    agent {label 'agent_win'}

options { disableConcurrentBuilds() }
    stages {
        stage ('Build on Windows'){
            agent {label 'agent_win'}
            steps {

            withMaven(
            jdk: 'openlogic-openjdk-8u352-b08-windows',
            maven: 'apache-maven-3.5.0-win')  {
                
                bat "mvn package"
               
                zip zipFile: "${JOB_NAME}win${BUILD_NUMBER}.zip",
                glob : "${WORKSPACE}\\target\\lavagna-jetty-console.war",
               
}
            stash includes:"${JOB_NAME}win${BUILD_NUMBER}.zip",
            name: "${JOB_NAME}"
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

                zip zipFile: "${WORKSPACE}/build/${JOB_NAME}lin${BUILD_NUMBER}.zip",
                glob : "${WORKSPACE}/target/lavagna-jetty-console.war",
                overwrite : true
            }

        }
        }
        stage ('Deploy artifact') {
            
            agent {label 'agent_lin'}

            options { skipDefaultCheckout()}

            environment {ARTIFACTORY_ACCESS_TOKEN = credentials('artifactory-access-token')}

            steps {
            
            dir("${WORKSPACE}/build/") {
            unstash 'win'
                }

            sh "jf rt upload --url http://192.168.31.13:8082/artifactory --access-token $ARTIFACTORY_ACCESS_TOKEN   ${WORKSPACE}/build/${JOB_NAME}lin${BUILD_NUMBER}.zip SNAPSHOTS/"
            sh "jf rt upload --url http://192.168.31.13:8082/artifactory --access-token $ARTIFACTORY_ACCESS_TOKEN   ${WORKSPACE}/build/${JOB_NAME}win${BUILD_NUMBER}.zip  SNAPSHOTS/"
            }
        post {
        always {
            cleanWs()
        }
        }
            }

}
    }