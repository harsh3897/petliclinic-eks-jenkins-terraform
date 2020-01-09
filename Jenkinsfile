pipeline {
    agent any
       triggers {
        pollSCM "* * * * *"
       }
    stages {
        stage('Build Application') { 
            steps {
                echo '=== Building Petclinic Application ==='
                sh 'mvn -B -DskipTests clean package' 
            }
        }
        stage('Test Application') {
            steps {
                echo '=== Testing Petclinic Application ==='
                sh 'mvn test'
            }
            post {
                always {
                    junit 'target/surefire-reports/*.xml'
                }
            }
        }
        stage('Build Docker Image') {
            when {
                branch 'master'
            }
            steps {
                echo '=== Building Petclinic Docker Image ==='
                script {
                    app = docker.build("645385727312.dkr.ecr.us-east-1.amazonaws.com/petclinic-spinnaker-jenkins")
                }
            }
        }
        stage('Push Docker Image') {
            when {
                branch 'master'
            }
            steps {
                echo '=== Pushing Petclinic Docker Image ==='
                script {
                    docker.withRegistry('https://645385727312.dkr.ecr.us-east-1.amazonaws.com', 'ecrCredentials') {
                        docker.image("645385727312.dkr.ecr.us-east-1.amazonaws.com/petclinic-spinnaker-jenkins").push()
                    }
                }
            }
        }
        stage('Remove local images') {
            steps {
                echo '=== Delete the local docker images ==='
                sh("docker rmi -f 645385727312.dkr.ecr.us-east-1.amazonaws.com/petclinic-spinnaker-jenkins:latest || :")
            }
        }
    }
}
