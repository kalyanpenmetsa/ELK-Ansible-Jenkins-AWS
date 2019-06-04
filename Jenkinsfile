#!/usr/bin/env groovy

node("master") {
	withEnv(["PATH=${env.PATH}:${tool 'ansible'}", "ANSIBLE_HOME=${tool 'ansible'}"]) {
		stage('Checkout') {
			checkout scm
		}

		def EC2Params = readProperties file: "/home/ec2-user/params.txt"
		def TargetAMIId = EC2Params['TargetAMIId']
		def TargetInstanceSize = EC2Params['TargetInstanceSize']
		def KeyPairName = EC2Params['KeyPairName']
		def InstanceSecurityGroup = EC2Params['InstanceSecurityGroup']
		def SubnetId = EC2Params['SubnetId']

		stage('Create EC2') {
      echo "EC2 Instance will be created using AWS CLI"
      sh "/usr/local/bin/aws ec2 run-instances --image-id ${TargetAMIId} --count 1 --instance-type ${TargetInstanceSize} --key-name ${KeyPairName} --region us-east-2 --security-group-ids ${InstanceSecurityGroup} --subnet-id ${SubnetId} >> ${WORKSPACE}/output.json"
		}
	  stage('ELK Installation') {
		  echo "Ansible playbook for ELK installation will be executed here..."
			def privateIPAddr = sh(returnStdout: true, script: "cat output.json | grep PrivateIpAddress | tail -1 | awk '{ print \$2 }' | tr -d '\"'").trim()
      sh "echo ${privateIPAddr} >> ${WORKSPACE}/hosts"
			sleep(time:300,unit:"SECONDS")
			ansiblePlaybook credentialsId: 'private_key', disableHostKeyChecking: true, inventory: 'hosts', playbook: 'deploy.yml'
		}
  }
}
