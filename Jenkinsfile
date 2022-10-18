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
                powershell "Rename-Item C:\jenkins\workspace\test_maven_main\target\lavagna-1.1.10-SNAPSHOT-distribution.zip lavagna-1.1.10-SNAPSHOT-distribution-win.zip"
                
                stash includes: 'target/lavagna-1.1.10-SNAPSHOT-distribution.zip', name: 'binary'
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
                unstash 'binary'
            }
        }

    }

}
