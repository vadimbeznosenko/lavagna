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
                bat "mvn package"
               
                zip zipFile: "win${BUILD_NUMBER}.zip",
                glob : 'C:\\jenkins\\workspace\\test_maven_main_2\\target\\lavagna-jetty-console.war',
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
            agent {
                label 'agent_lin'
            }
            steps {
                withEnv (["PATH+MAVEN=${tool 'apache-maven-3.5.0-lin'}/bin",
                "JAVA_HOME=${tool 'java/jdk-8u202-linux'}",
                "MAVEN_HOME=${tool 'apache-maven-3.5.0-lin'}"]){
                sh "echo $JAVA_HOME"
                sh "echo $MAVEN_HOME"
                sh "echo $PATH"    
                sh 'mvn clean'
                sh 'mvn package'

                zip zipFile: "/var/lib/jenkins/workspace/test_maven_main_2/build/lin64/lin${BUILD_NUMBER}.zip",
                glob : '/var/lib/jenkins/workspace/test_maven_main_2/target/lavagna-jetty-console.war',
                overwrite : true
            }

        }
        }
        stage ('Deploy artifact') {
            agent {
                label 'agent_lin'
            }

            options { skipDefaultCheckout()}

            steps {
            withEnv (["ARTIFACTORY_ACCESS_TOKEN=${credentials 'artifactory-access-token'}"]){
            dir('/var/lib/jenkins/workspace/test_maven_main_2/build/win64/') {
            unstash 'binarywin'
                }

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