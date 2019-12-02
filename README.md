# ansible
Prerequiesites:
Jenkins pipeline
pipeline {
    agent any
   stages {

    stage('1. Install docker on remote mashines'){
      steps {
        echo "Pull down any existing Ansible code for this pipeline's Workspace..."
         sh """
              # set environment variable for d'ont prompting the known hosts
              export ANSIBLE_HOST_KEY_CHECKING=False
              #Clear old Workspace directory - VERY important to make sure no old Terraform code or config files are hanging around!
              rm -rf ansible
              #Pull down current Workspace code
              git clone -b host_network https://github.com/vasylw/ansible.git
              ansible -i /var/lib/jenkins/workspace/Build_IS_Terraform/cartsservice/hosts all -m ping
              ansible -i /var/lib/jenkins/workspace/Build_IS_Terraform/cartsservice/hosts all --become -m raw -a "apt-get update -y" -u ubuntu
              ansible -i /var/lib/jenkins/workspace/Build_IS_Terraform/cartsservice/hosts all --become -m raw -a "apt install -y python-minimal" -u ubuntu
              ansible -i /var/lib/jenkins/workspace/Build_IS_Terraform/cartsservice/hosts all --become -m raw -a "apt install -y python3-pip" -u ubuntu
              ansible -i /var/lib/jenkins/workspace/Build_IS_Terraform/cartsservice/hosts all --become -m raw -a "pip3 install docker" -u ubuntu
              ansible-playbook -i /var/lib/jenkins/workspace/Build_IS_Terraform/cartsservice/hosts /var/lib/jenkins/workspace/Ansible_python_docker/ansible/docker_install.yml
              ansible-playbook -i /var/lib/jenkins/workspace/Build_IS_Terraform/cartsservice/hosts /var/lib/jenkins/workspace/Ansible_python_docker/ansible/docker_run_db.yml
              ansible-playbook -i /var/lib/jenkins/workspace/Build_IS_Terraform/cartsservice/hosts /var/lib/jenkins/workspace/Ansible_python_docker/ansible/docker_run_app.yml
              """
        }
      }
      
  } //stages
} //pipeline
