pipelineRepo = "https://github.com/ymarutiprasad/maven-wrapper.git"

node {
   def mvnHome
   stage('Preparation') { // for display purposes
      // Get some code from a GitHub repository
      //git 'https://github.com/ymarutiprasad/maven-wrapper.git'
      git url: pipelineRepo, credentialsId: "git-credentials", branch: 'master'
      // Get the Maven tool.
      // ** NOTE: This 'M3' Maven tool must be configured
      // **       in the global configuration.           
      mvnHome = tool 'M3'
   }
   stage('Build') {
      // Run the maven build
      if (isUnix()) {
         //sh "'${mvnHome}/bin/mvn' clean install"
         sh "bash mvnw clean install"
      } else {
         bat(/"${mvnHome}\bin\mvn" clean install/)
      }
   }
   stage('Results') {
      junit '**/target/surefire-reports/TEST-*.xml'
      archive 'target/*.jar'
   }
   
   stage('Create virtualenv') {
      
        dir('ansible') {
          sh 'virtualenv venv'
          sh 'venv/bin/pip install ansible'
        }
      
    }
    
    stage('Run Ansible deploy') {
        dir('ansible') {
          sh  "export ANSIBLE_HOST_KEY_CHECKING=False;bash -c 'source venv/bin/activate && ansible-playbook " +
              "-i localhost, " +
              "-u vagrant " +
              "--key-file='~/.ssh/id_rsa' " +
              "deploy-app.yml'"
        }
    }
}
