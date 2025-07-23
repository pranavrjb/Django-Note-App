pipeline {
    agent {label "node1"}

    stages {
        stage('Code') {
            steps {
                echo 'cloning the code my github.'
                git url: "https://github.com/pranavrjb/Django-Note-App.git", branch: "main"
                echo "Cloned Successfully!"
            }
        }
        stage('Build') {
            steps {
                echo 'Building the django code.'
                sh "docker build -t notes-app:latest ."
                echo "Successfully Build!"
            }
        }
        stage('Test') {
            steps {
                echo 'Testing the project.'
            }
        }
          stage("Pushing Images"){
            steps{
                echo 'Pushing images in the registry'
                withCredentials([usernamePassword(
                    'credentialsId': "dockerHubCred",
                    passwordVariable: "dockerHubPass",
                    usernameVariable: "dockerHubUser")]){
                        sh "docker login -u ${env.dockerHubUser} -p ${env.dockerHubPass}"
                        sh "docker image tag notes-app:latest ${env.dockerHubUser}/notes-app:v1"
                        sh "docker push ${env.dockerHubUser}/notes-app:v1"
                    }
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying the apps'
                sh 'docker compose down && docker compose up -d'
                echo "App has been deployed!"
            }
        }
      
    }
}
