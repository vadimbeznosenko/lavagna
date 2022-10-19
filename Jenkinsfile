pipeline {
    agent none 
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
                powershell "mvn clean"
                powershell "mvn package"                
                stash includes: 'target/lavagna-1.1.10-SNAPSHOT-distribution.zip', name: 'binary_win'
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
                stash includes: '/var/lib/jenkins/workspace/test_maven_main/target/lavagna-1.1.10-SNAPSHOT-distribution.zip', name: 'binary_lin'
                dir('/var/lib/jenkins/workspace/test_maven_main/windows_art') {
                unstash 'binary_win'
        }
        dir('/var/lib/jenkins/workspace/test_maven_main/lin_art') {
                unstash 'binary_lin'
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
