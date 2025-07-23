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
        stage('Deploy using Ansible') {
            steps {
                echo 'Deploying the apps using ansible Playbook'
                 withCredentials([sshUserPrivateKey(
                    credentialsId: 'ansible',
                    keyFileVariable: 'ANSIBLE_KEY'
                )]) {
                        sh """
                        ssh -i "/var/lib/jenkins/keys/id_rsa" -o StrictHostKeyChecking=no vagrant@192.168.56.2'
                            rm -rf /home/vagrant/project
                            mkdir -p /home/vagrant/project
                            git clone https://github.com/pranavrjb/Ansible.git /home/vagrant/project
                            source /home/vagrant/myenv/bin/activate
                            cd /home/vagrant/project/ansible &&
                            ansible-galaxy collection install community.docker
                            ansible-playbook deploy_playbook.yaml -i inventory -e "build_number=${BUILD_NUMBER}"
                        '
                        """
                }
                echo "App has been deployed!"
            }
        }
      
    }
}
