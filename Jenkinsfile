pipeline {
    
    agent any

	environment {	
		DOCKERHUB_CREDENTIALS=credentials('dockerloginid')
	} 
 
    tools 
    {
        maven 'maven 3.8.6'
    }
    
    stages {
        stage('SCM Checkout') {
            steps {
				git 'https://github.com/sanjay-2307/MyRepo.git'
            }
		}
		
        stage('Maven Build') {
            steps {
                // Run Maven on a Unix agent.
                sh "mvn -Dmaven.test.failure.ignore=true clean package"
            }
		}

        stage("Docker build"){
            steps {
				sh 'docker version'
				sh 'docker build -t myapp .'
				sh 'docker image list'
				sh 'docker tag myapp sanjay2307/myapp:v1.0'
            }
        }
		stage('Login2DockerHub') {

			steps {
				sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
			}
		}

		stage('Push2DockerHub') {

			steps {
				sh 'docker push myapp sanjay2307/myapp:v1.0'
			}
		}

		stage("Deploy to K8s Cluster") {
            steps {
                sshPublisher(publishers: [sshPublisherDesc(configName: 'KMaster', transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: 'sudo kubectl apply -f k8smvndeployment.yaml', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: '.', remoteDirectorySDF: false, removePrefix: '', sourceFiles: '*.yaml')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])
    		}
        }
	}
}
