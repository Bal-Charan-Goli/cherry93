def ansible = [:]
         ansible.name = 'ansible'
         ansible.host = '172.31.44.14'
         ansible.user = 'ec2-user'
         ansible.password = '8686865755'
         ansible.allowAnyHosts = true
def kops = [:]
         kops.name = 'kops'
         kops.host = '172.31.38.200'
         kops.user = 'ec2-user'
         kops.password = '8686865755'
         kops.allowAnyHosts = true
pipeline {
    agent { label 'build-server'}

    tools {
        // Install the Maven version configured as "M3" and add it to the path.
        maven "maven3.8"
    }

    stages {
        stage('Prepare-Workspace') {
            steps {
                // Get some code from a GitHub repository
                git credentialsId: 'github-server-credentials', url: 'https://github.com/Bal-Charan-Goli/cherry93.git'    
		stash 'Source'
            }
            
        }
        stage('Tools-Setup') {
            steps {
		    echo "Tools Setup"
                sshCommand remote: ansible, command: 'cd cherry93; git pull'
                sshCommand remote: ansible, command: 'cd cherry93; ansible-playbook -i hosts tools/sonarqube/sonar-install.yaml'
                sshCommand remote: ansible, command: 'cd cherry93; ansible-playbook -i hosts tools/docker/docker-install.yml'   
                     
                //K8s Setup
                sshCommand remote: kops, command: "cd cherry93; git pull"
	        sshCommand remote: kops, command: "kubectl apply -f cherry93/k8s-code/staging/namespace/staging-ns.yml"
	        sshCommand remote: kops, command: "kubectl apply -f cherry93/k8s-code/prod/namespace/prod-ns.yml"
            }            
        }
	    
    }
}
