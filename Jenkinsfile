pipeline {
agent any
tools {
        // Install the Maven version configured as "M3" and add it to the path.
        maven "Maven"
    }

    stages {
        stage('Code checkout') {
            steps {
                // Get some code from a GitHub repository
                git branch: 'Devops', url: 'https://github.com/pramod-devops/git-repo22.git'

                // Run Maven on a Unix agent.
                sh 'mvn -Dmaven.test.failure.ignore=true install'
            }

        stage('Results') {
            steps {
                 junit '**/target/surefire-reports/*.xml'
                 archiveArtifacts 'target/*.war'
           }
         }
        }
    }

       stage('Sonarqube') {
             environment {
             scannerHome = tool 'sonarqube'
           }
           steps {
              withSonarQubeEnv('sonarqube') {
            sh "${scannerHome}/bin/sonar-scanner"
        }
		}
        stage('Artifact upload') {
            steps {
		     nexusPublisher nexusInstanceId: '1234', nexusRepositoryId: 'releases', packages: [[$class: 'MavenPackage', mavenAssetList: [[classifier: '', extension: '', filePath: 'target/helloworld.war']], mavenCoordinate: [artifactId: 'hello-world-servlet-example', groupId: 'com.geekcap.vmturbo', packaging: 'war', version: '$BUILD_NUMBER']]]
            }
        }
      }
	 stage('Deploy War') {
      steps {
        sh label: '', script: 'ansible-playbook deploy.yml'
            }
           		   }
	
     post {
        success {
            mail to:"pramodborse121@gmail.com", subject:"SUCCESS: ${currentBuild.fullDisplayName}", body: "Build success"
        }
        failure {
            mail to:"pramodborse121@gmail.com", subject:"FAILURE: ${currentBuild.fullDisplayName}", body: "Build failed"
                 }  
          }
 }	
