# Java_Jenkins_ecr_eks

> Following this link :: https://www.coachdevops.com/2022/01/deploy-springboot-microservices-app_11.html

##3 ways to create cluster using aws eks -->
###i. installing eksctl and using the 


>>    eksctl create cluster \
      --name clustername \
      --version 1.2 \


ii.  using aws management console --> from the web ui

iii. AWS CLI

>>    aws eks create cluster \
      --region region-code
      --name cluster_name
      --kubernetes-version 1.21 \ 
      --role-arn arn:aws::
==========================================================================================================================================

EC2 Machine Configuration::
i. JenkinsMachine_ecr-eks ==>
        t2.medium
        ami: ami-0884d2865dbe9de4b
        zone: us-east-2b
        ubuntu 22.04
        8Gib gp2
        SG: ecr-eksSG
          port: 22, 80, 8080
        --------
    userData::
          sudo hostname jenkins
          sudo apt update
          sudo apt install default-jdk   OR    sudo apt install openjdk-17-jdk
          java -version
          sudo apt install maven -y
          mvn --version
        Jenkins setup
          curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key | sudo tee \        #importing gpg key
                /usr/share/keyrings/jenkins-keyring.asc > /dev/null
          echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \                          #Add the Jenkins software repository to source list and provide the authentication key
               https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
               /etc/apt/sources.list.d/jenkins.list > /dev/null
          sudo apt install jenkins -y                                                              # install jenkins
          sudo systemctl status jenkins
          sudo systemctl start jenkins    OR service jenkins start
          sudo systemctl enable jenkins
          sudo ufw allow 8080                                                                       # modify firewall -Allow Jenkins to communicate by setting up the default UFW firewall.
          sudo ufw status
          sudo ufw enable

        On webBrowser::   http://<ec2_ip_address>:8080


ii. eksctl                                                                                          # either can create another instance or on jenkins machine
    userData::
      AWSCLI
          curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
          sudo apt install unzip
          sudo unzip awscliv2.zip  
          sudo ./aws/install
          aws --version
      EKSCTL
            ARCH=$(uname -m)
            PLATFORM=$(uname -s)
            curl -sLO "https://github.com/eksctl-io/eksctl/releases/latest/download/eksctl_${PLATFORM}_${ARCH}.tar.gz"
            tar -xzf eksctl_${PLATFORM}_${ARCH}.tar.gz -C /tmp
            sudo mv /tmp/eksctl /usr/local/bin
            rm eksctl_${PLATFORM}_${ARCH}.tar.gz

            eksctl version
      Installing KubeCTL
            curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
            sudo apt-get update
            sudo apt-get install -y apt-transport-https ca-certificates curl gnupg
            curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
            sudo sh -c 'echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" > /etc/apt/sources.list.d/kubernetes.list'
            sudo apt-get install -y kubectl
            kubectl version --client


      
      VARIKABLES:
            AWS_ACCESS_KEY = ""
            AWS_SECRET_KEY = ""
            REGION = "us-east-1"
            KEY_NAME = ""
            JENKINS_MACHINE_IP = ""
            
      AWS Configure
                  AWS_ACCESS_KEY_ID = $AWS_ACCESS_KEY
                  AWS_SECRET_ACCESS_KEY = $AWS_SECRET_KEY
                  AWS_DEFAULT_REGION = $REGION
                  
                  # Configure AWS CLI
                  aws configure set aws_access_key_id $AWS_ACCESS_KEY_ID
                  aws configure set aws_secret_access_key $AWS_SECRET_ACCESS_KEY
                  aws configure set default.region $AWS_DEFAULT_REGION
                  
                  # Verify configuration
                  aws configure list
      SSH-keygen
            ssh-keygen -t rsa -b 4096 -f ~/.ssh/id_rsa -N ""
       --------
      CREATE eksctl cluster 
            eksctl create cluster --name eksClusterName --region us-east-2 --nodegroup-name NodeGroup --node-type t3.small --manged --nodes 2
            eksctl get cluster --name eksClusterName --region us-east-2


iii. ECR on AWS Web UI
         Installing Docker --> either be done on separate machine or on jenkins machine 
            sudo apt install gnupg2 pass -y
            sudo apt install docker.io -y 
            systemctl start docker
            systemctl enable docker
            systemctl status docker

###If installing on same machine for all can do it by creating different users and their respective user groups
      sudo useradd docker
      sudo usermod -aG docker $USER
      newgrp docker
      sudo usermod -a -G docker jenkins                  # adding jenkins user to docker group as well 
      systemctl daemon-reload


PLUGINS in JENKINS
      docker
      docker-pipeline
      kubernetes cli




==========================================================================================================================================


==========================================================================================================================================


==========================================================================================================================================
