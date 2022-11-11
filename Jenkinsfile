pipeline {
    agent {label 'agent_win'}

options { disableConcurrentBuilds() }
    stages {
        stage ('Build on Windows'){
            agent {
                label 'agent_win'
            }
            steps {
                withEnv (["PATH+MAVEN=${tool 'apache-maven-3.5.0-win'}/bin",
                "JAVA_HOME=${tool 'openlogic-openjdk-8u352-b08-windows'}",
                "MAVEN_HOME=${tool 'apache-maven-3.5.0-win'}"]) {
               
                bat "set JAVA_HOME"
                bat "set MAVEN_HOME"
                
                bat "mvn clean"
                bat "mvn package"
               
                zip zipFile: "win${BUILD_NUMBER}.zip",
                glob : "${WORKSPACE}\\target\\lavagna-jetty-console.war",
                overwrite : true
               
                stash includes: "win${BUILD_NUMBER}.zip",
                name: 'binarywin'
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
                withEnv (["PATH+MAVEN=${tool 'apache-maven-3.5.0-lin'}/bin",
                "JAVA_HOME=${tool 'java/jdk-8u202-linux'}",
                "MAVEN_HOME=${tool 'apache-maven-3.5.0-lin'}"]){

                sh "echo $JAVA_HOME"
                sh "echo $MAVEN_HOME"
                sh "echo $PATH"    
                sh 'mvn clean'
                sh 'mvn package'

                zip zipFile: "${WORKSPACE}/build/lin64/lin${BUILD_NUMBER}.zip",
                glob : "${WORKSPACE}/target/lavagna-jetty-console.war",
                overwrite : true
            }

        }
        }
        stage ('Deploy artifact') {
            
            agent {label 'agent_lin'}

            options { skipDefaultCheckout()}

            steps {
            withEnv (["ARTIFACTORY_ACCESS_TOKEN=${credentials 'artifactory-access-token'}"]){
            
            sh "echo ${WORKSPACE}"

            dir("${WORKSPACE}/build/win64/") {
            unstash 'binarywin'
                }
            sh "echo $ARTIFACTORY_ACCESS_TOKEN"

            sh "jf rt upload --url http://192.168.31.13:8082/artifactory --access-token $ARTIFACTORY_ACCESS_TOKEN   build/lin64/lin${BUILD_NUMBER}.zip  SNAPSHOTS/"
            sh "jf rt upload --url http://192.168.31.13:8082/artifactory --access-token $ARTIFACTORY_ACCESS_TOKEN   build/win64/win${BUILD_NUMBER}.zip  SNAPSHOTS/"
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