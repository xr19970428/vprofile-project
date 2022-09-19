pipeline {
    agent any
    tools {
	    maven "MAVEN3"
	    jdk "OracleJDK8"
	}

    environment {
        registryCredential = 'ecr:ap-southeast-2:awscreds'
        appRegistry = "200167892154.dkr.ecr.ap-southeast-2.amazonaws.com/vprofileappimg"
        vprofileRegistry = "https://200167892154.dkr.ecr.ap-southeast-2.amazonaws.com"
    }
  stages {
   stage('Fetch code'){
      steps {
        git branch: 'docker', url: 'https://github.com/xr19970428/vprofile-project.git'
      }
    }
	  
    stage('Test'){
      steps {
        sh 'mvn test'
      }
    }

    stage ('CODE ANALYSIS WITH CHECKSTYLE'){
            steps {
                sh 'mvn checkstyle:checkstyle'
            }
            post {
                success {
                    echo 'Generated Analysis Result'
                }
            }
        }

     
    stage('Build App Image') {
       steps {
       
         script {
                dockerImage = docker.build( appRegistry + ":$BUILD_NUMBER", "./Docker-files/app/multistage/")
             }

     }
    
    }

    stage('Upload App Image') {
          steps{
            script {
              docker.withRegistry( vprofileRegistry, registryCredential ) {
                dockerImage.push("$BUILD_NUMBER")
                dockerImage.push('latest')
              }
            }
          }
     }

  }
}
