pipeline {
    agent none 
  
    stages {
        stage ('Test on Linux') {
            agent{
                label 'agent_lin'
            }
        tools{
        maven '3.5.0'
        jdk 'openlogic-openjdk-jre-11.0.15+10-linux'
            }
        environment {
        JAVA_HOME="${tool 'openlogic-openjdk-jre-11.0.15+10-linux'}"
        MAVEN_HOME="${tool '3.5.0'}"
    }
            steps {
                sh 'ls'
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
        jdk 'openlogic-openjdk-jre-11.0.15+10-windows'
            }
        environment {
        JAVA_HOME="${tool 'openlogic-openjdk-jre-11.0.15+10-windows'}"
        MAVEN_HOME="${tool '3.5.0'}"
        }
            steps {
                powershell "dir"
                
            }
            }
    }
}
