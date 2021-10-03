# Packer AMI & IAC Terraform EC2 setup & Jenkins Pipeline to setup EKS, Infra platform and Run Demo-App 

### Prerequisite: Linux ubuntu 20, git
1. Clone the github repo in your ubuntu machine:
   ```
   $ git clone https://github.com/satender346/packer-ami-with-jenkins.git
   $ git clone https://github.com/satender346/IaC-terraform-ec2.git
   ```
2. Change directory to 'packer_ami_with_jenkins' and Install packer by running the shell script 
    ```
     $ cd packer_ami_with_jenkins
     $ install_packer.sh
     $ cd ..
   ```
3. Change directory to 'IaC-terraform-ec2' and Install terraform and aws-cli by running the shell script
    ``` 
      $ cd IaC-terraform-ec2
      $ steps_to_install_terraform_and_awsCli.sh
      $ cd ..
   ```
4. export aws scerets on the terminal.(find aws secrets on the aws account ui) Run Below commands to build ami-image:
   ```
   $ cd packer_ami_with_jenkins
   $ export AWS_ACCESS_KEY_ID=
   $ export AWS_SECRET_ACCESS_KEY=
   $ export AWS_DEFAULT_REGION=us-west-1
   $ packer init .
   $ packer build .
   $ cd ..
   ```
5. Change directory to 'IaC-terraform-ec2' and Update the variables.tf with your vpc_id, subnet_id, ssh_key:optional, linux_ami_id and aws_region.(# Check your aws ec2 home for these values) you can pass your machine ssh key in the ssh_key variable.
   ```
     $ cd IaC-terraform-ec2
     $ vi variables.tf
   ```
6. Run below commands to setup an aws-instance for the eks deployment:
   ```
   $ terraform init
   $ terraform plan --auto-approve
   $ terraform apply --auto-approve
   ```
7. Once instance finish it will give public ip. Copy that ip, because instance and jenkins will be running on that ip. \
   a. open jenkins in browser using instance-ip:8080 \
   b. Login to instance using ssh devops@instance-public-ip and provide password 'devops' \
   c. To login to jenkins, username will be 'admin' and get initial password by running the below command into the instance terminal:
     ```
     $ sudo cat /var/jenkins_home/secrets/initialAdminPassword
     ```
   d. from jenkins ui click build button for all below jobs in the same order one by one.
       IaC-eks-cluster-pipeline : Job will create EKS cluster in AWS
       IaC-platform-deployment : Job will deploy infra ( Consul, Vault, jenkins, sonarqube, Nexus, Prometheus, Grafana, Jaeger)
       Micro-services-CI-CT-CD: Job will deploy sample GO-React application. which cab be access on instance-ip:30070
      ```
      1 IaC-eks-cluster-pipeline
      2 IaC-platform-deployment
      3 Micro-services-CI-CT-CD
      ```
   e. Iac-eks-cluster pipeline outputs the kubernetes config, copy and save that config on the instance machine and you can access the kubernetes cluster.
   f. App will be running on instance-ip:30070
