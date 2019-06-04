#!/usr/bin/env groovy

node("master") {
	withEnv(["PATH=${env.PATH}:${tool 'ansible'}", "ANSIBLE_HOME=${tool 'ansible'}"]) {
		stage('Checkout') {
			checkout scm
		}

    stage('AWS CLI Setup') {
			sh "curl \"https://s3.amazonaws.com/aws-cli/awscli-bundle.zip\" -o \"awscli-bundle.zip\""
      sh "sudo yum install unzip -y"
      sh "yes | unzip awscli-bundle.zip"
      sh "sudo ./awscli-bundle/install -i /usr/local/aws -b /usr/local/bin/aws"
		}

		stage('Get Parameters'){

		}


		stage('Create EC2') {
        echo "EC2 Instance will be created using AWS CLI"
        sh "/usr/local/bin/aws ec2 run-instances --image-id $TargetAMIId --count 1 --instance-type $TargetInstanceSize --key-name $KeyPairName --region us-east-2 --security-group-ids $InstanceSecurityGroup --subnet-id $SubnetId >> ${WORKSPACE}/output.json"
        sh "grep 'PrivateIpAddress' sample.json | tail -1 | awk '{ print \$2 }' | tr -d '\"' > ipaddr.txt"
        EC2_IP = readFile('ipaddr.txt').trim()
		}
	  stage('ELK Installation') {
		  echo "Ansible playbook for ELK installation will be executed here..."
      sh "echo ${env.EC2_IP} >> ${WORKSPACE}/hosts"
			ansiblePlaybook credentialsId: 'private_key', disableHostKeyChecking: true, inventory: 'hosts', playbook: 'deploy.yml'
		}
  }

}
