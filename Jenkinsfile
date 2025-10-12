pipeline {
    agent any

    triggers {
        githubPush()
    }

    stages {
        stage('Job 1: Install Puppet Agent') {
            steps {
                sshagent (credentials: ['jenkins-ssh']) {
                    sh '''
                    ssh -o StrictHostKeyChecking=no ubuntu@172.31.7.195 "sudo apt update && sudo apt install -y puppet-agent"
                    '''
                }
            }
        }

        stage('Job 2: Install Docker via Ansible') {
            steps {
                sshagent (credentials: ['jenkins-ssh']) {
                    sh '''
                    ansible-playbook /home/ubuntu/ansible/install_docker.yml
                    '''
                }
            }
        }

        stage('Job 3: Build & Deploy PHP Docker Container (Nginx)') {
            steps {
                sshagent (credentials: ['jenkins-ssh']) {
                    sh '''
                    ssh ubuntu@172.31.x.x '
                      cd /home/ubuntu &&
                      sudo rm -rf website &&
                      git clone -b master https://github.com/IshanPalkar/projCert.git website &&
                      cd website/website &&
                      sudo docker build -t php-nginx-app:latest . &&
                      sudo docker rm -f php-nginx-app || true &&
                      sudo docker run -d -p 80:80 --name php-nginx-app php-nginx-app:latest
                    '
                    '''
                }
            }
        }
    }

    post {
        failure {
            sshagent (credentials: ['jenkins-ssh']) {
                sh '''
                ssh ubuntu@172.31.7.195 "sudo docker rm -f php-nginx-app || true"
                '''
            }
        }
    }
}

