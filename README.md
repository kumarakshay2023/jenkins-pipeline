pipeline{
    agent any
    stages{
        stage("git checkout"){
            steps {
                echo "cloning the repo"
                git 'https://github.com/kumarakshay2023/healthcare-akshay.git'
                
            }
        }
           stage("Package code"){
            steps {
                echo "Packaging the code"
                sh "mvn clean package"
                
            }
        }
          stage("Building the docker image"){
            steps {
                echo "Building image"
                sh "docker build -t akshaykumar7903/healthcare-akshay:1 ."
                
            }
        }
         stage("Docker login"){
            steps {
                echo "Login to docker"
               withCredentials([string(credentialsId: 'dockerhubpass', variable: 'docker')]) {
         sh "docker login -u akshaykumar7903 -p ${docker}"
   }
                
            }
        }
        stage("Pushing the image to docker hub"){
            steps {
                echo "Pushing the image"
                sh "docker push akshaykumar7903/healthcare-akshay:1"
                
            }
        }
          stage("Executing ansible playbook file"){
            steps{
                ansiblePlaybook become: true, credentialsId: 'ansible-test', disableHostKeyChecking: true, installation: 'ansible', inventory: '/etc/ansible/hosts', playbook: 'ansible-playbook.yml', vaultTmpPath: ''
            }
        }
    }
}
