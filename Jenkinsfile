pipeline { 
    agent any
    tools {
        maven 'Maven'
    } 
       
    stages{
        stage("Test"){
            steps{
            // mvn test
                sh "mvn test"
                echo "========executing A========"
            }
        }
        stage("Build"){
            steps{
                sh "mvn package"
                echo "========executing A========"
            }
        }
        stage("Deploy on test"){
            steps{
                // deploy on test server
                deploy adapters: [tomcat7(credentialsId: 'c8fbadfd-7b6b-4012-84e3-297b1915e4fd', path: '', url: 'http://192.168.184.153:8080/')], contextPath: '/app', war: '**/*.war'
                echo "========executing A========"
            }
        }
        stage("Deploy on prod"){
            steps{
                deploy adapters: [tomcat9(credentialsId: 'c8fbadfd-7b6b-4012-84e3-297b1915e4fd', path: '', url: 'http://192.168.184.152:8080/')], contextPath: '/app', war: '**/*.war'
                echo "========executing A========"
            }
        }
    }
    post{
        always{
            echo "========always========"
        }
        success{
            echo "========pipeline executed successfully ========"
        }
        failure{
            echo "========pipeline execution failed========"
        }
    }
}
