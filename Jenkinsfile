pipeline {
    environment{
        dockerImage = ''
    }
    agent any
    tools {
        maven "Maven"
        git "Git"
        jdk "Jdk"
	dockerTool "Docker"
    }

    stages {
        stage('1.Code Build and Analysis of Code') {
            steps {
				echo '-----------------Building code-----------------------'
                
                
                withSonarQubeEnv('sonarserver'){
                    withMaven(maven:'Maven'){
                        bat 'mvn clean package sonar:sonar'
                        echo '---------------Code Build and analysis of code is successfull---------------------'
                    }
				}
            }

           
    	}


		stage('Quality Gate Check'){
			steps{
				timeout(time: 1, unit: 'HOURS') {
                    waitForQualityGate abortPipeline: true
                }
			}
		}

        stage('3.Building image') {
            steps{
                script {
                    echo '---------------------------Building Image----------------------------------'
                    bat 'docker build . -t cloudbased-deployment'
                    echo '---------------------------Image Successfully Build---------------------------------'
		            bat 'docker images'
                }
            }
        }

        stage('4.Deploying image to ECR') {
            steps{
                script{
                    echo '-----------------------------Deploying Image----------------------------------------'
                    docker.withRegistry('https://795361990663.dkr.ecr.us-east-1.amazonaws.com', 'ecr:us-east-1:aws-credentials') {
                        bat 'docker push 795361990663.dkr.ecr.us-east-1.amazonaws.com/cloudbased-deployment:latest'
                        echo '-------------------------Image Successfully pushed--------------------------------'
                    }
                } 
            }
        }
	}

}
