pipeline {
    agent none

environment {
            CI = true
            ARTIFACTORY_ACCESS_TOKEN = credentials('artifactory-access-token')
  }

    stages {
        stage ('Build on Windows'){
            agent {
                label 'agent_win'
            }
               tools{
        maven '3.5.0'
        jdk 'Java_8'
            }

            steps {
                 catchError{
                bat "mvn clean"
                bat "mvn package"
                zip zipFile: "win${BUILD_NUMBER}.zip",  glob : 'C:\\jenkins\\workspace\\test_maven_main_2\\target\\lavagna-jetty-console.war'
                stash includes: "win${BUILD_NUMBER}.zip", name: 'binarywin'
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
        tools {
        maven '3.5.0'
        jdk 'openlogic-openjdk-8u342-b07-linux'
            }
            steps {
                sh "PATH=$PATH:$JAVA_HOME/bin"
                sh 'mvn clean'
                sh 'mvn package'
                zip zipFile: "/var/lib/jenkins/workspace/test_maven_main_2/build/lin64/lin${BUILD_NUMBER}.zip", glob : '/var/lib/jenkins/workspace/test_maven_main_2/target/lavagna-jetty-console.war', overwrite : true
                }
            }

        
        stage ('Deploy artifact') {
            agent {
                label 'agent_lin'
            }
            steps {
                dir('/var/lib/jenkins/workspace/test_maven_main_2/build/win64/') {
                unstash 'binarywin'
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
