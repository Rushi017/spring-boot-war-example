pipeline{
    agent any
    triggers {
        pollSCM 'H/2 * * * *'
		}
    tools{
        maven 'maven'
    }
    stages{
        stage('checkout the code'){
            steps{
                slackSend channel: 'jenkins-notification', message: 'job started'
                git url:'https://github.com/NavnathChaudhari/spring-boot-war-example.git', branch: 'master'
            }
        }
        stage('build the code'){
            steps{
                sh 'mvn clean package'
            }
        }
        stage("sonar quality check"){
            steps{
                script{
                    withSonarQubeEnv(credentialsId: '17') {
                            sh "mvn sonar:sonar -f /var/lib/jenkins/workspace/hello-world-cicd/pom.xml"
                    }
                    timeout(time: 1, unit: 'HOURS') {
                      def qg = waitForQualityGate()
                      if (qg.status != 'OK') {
                           error "Pipeline aborted due to quality gate failure: ${qg.status}"
                      }
                    }
                } 
            }
            }
        stage('create docker image'){
            steps{
                sh '''docker image build -t $JOB_NAME:v1.$BUILD_ID .
docker image tag $JOB_NAME:v1.$BUILD_ID rdeshpande17/$JOB_NAME:v1.$BUILD_ID
docker image tag $JOB_NAME:v1.$BUILD_ID rdeshpande17/$JOB_NAME:latest'''
            }

        }
        stage('push the image into docker hub'){
            steps{
                withCredentials([string(credentialsId: 'rdeshpande', variable: 'docker_pass')]) {
                    sh "docker login -u rdeshpande17 --password-stdin ${docker_pass}"

}
                    sh '''docker image push rdeshpande17/$JOB_NAME:v1.$BUILD_ID
docker image push rdeshpande17/$JOB_NAME:latest 
docker image rmi $JOB_NAME:v1.$BUILD_ID rdeshpande17/$JOB_NAME:v1.$BUILD_ID rdeshpande17/$JOB_NAME:latest'''

            }
        }
        stage('Deploy application on k8s'){
            steps{
                sh 'kubectl apply -f deployment.yaml'
        }
        }
    }
        post{
        always{
            echo "========always========"
        }
        success{
            echo "========pipeline executed successfully ========"
            slackSend channel: 'jenkins-notification', message: ' job success'
        }
        failure{
            echo "========pipeline execution failed========"
            slackSend channel: 'jenkins-notification', message: ' job failed'
        }
        }
    }
