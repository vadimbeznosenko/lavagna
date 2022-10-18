pipeline {
    agent none 
    stages {
        stage ('Test on Linux') {
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
            }
        }
        stage ('Test on Windows'){
            agent {
                label 'agent_win'
            }
               tools{
        maven '3.5.0'
        jdk 'openlogic-openjdk-8u342-b07-windows'
            }
            steps {
                powershell "mvn clean"
                powershell "mvn package"
                
            }
            }
    }
    
}
