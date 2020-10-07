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
                    sh "chmod 666 /var/run/docker.sock"
                    sh "docker pull ibuchh/petclinic-spinnaker-jenkins"
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
                    sh "docker tag ibuchh/petclinic-spinnaker-jenkins gcr.io/springpoc-291811/ibuchh/petclinic-spinnaker-jenkins:tag2"
                    sh "docker push gcr.io/springpoc-291811/ibuchh/petclinic-spinnaker-jenkins:tag2"
                }
            }
        }
        stage('Remove local images') {
            steps {
                echo '=== Delete the local docker images ==='
                sh("docker rmi -f ibuchh/petclinic-spinnaker-jenkins:latest || :")
                sh("docker rmi -f ibuchh/petclinic-spinnaker-jenkins:$SHORT_COMMIT || :")
            }
        }
    }
}
