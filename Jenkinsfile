pipeline {
     agent none 
     stages {
        stage ('Build on Linux') {
            agent {
                label 'agent_lin'
            }
        tools {
        maven '3.5.0'
        jdk 'openlogic-openjdk-8u342-b07-linux'}
            steps {
                sh "PATH=$PATH:$JAVA_HOME/bin"
                sh 'mvn clean'
                sh 'mvn package'
                zip zipFile: "lin${BUILD_NUMBER}.zip",  glob :/var/lib/jenkins/workspace/test_maven_main_2/target/lavagna-jetty-console.war
                stash includes: "lin${BUILD_NUMBER}.zip", name: 'binarylin'

        post { 
        always { 
            cleanWs()
        }
        }

    }
        }

    stages {
        stage ('deploy on Windows'){
            agent {
                label 'agent_win'
            }
               tools{
        maven '3.5.0'
        jdk 'Java_8'
            }
                        }
            environment {
            CI = true
            ARTIFACTORY_ACCESS_TOKEN = credentials('artifactory-access-token')
  }
            steps {
                dir('C:\\jenkins\\workspace\\test_maven_main_2\\build\\lin') {
                unstash 'binarylin'
                }
                 bat "jf rt upload --url http://192.168.31.13:8082/artifactory --access-token $ARTIFACTORY_ACCESS_TOKEN   build\\win64\\lin${BUILD_NUMBER}.zip  SNAPSHOTS/"
}            
        post { 
        always { 
            cleanWs()
        }
        }
            }

}
}