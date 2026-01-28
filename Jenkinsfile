pipeline {
    agent any
     tools{
         jdk 'Java17'
         maven 'Maven'
    }
    stages {
        stage('Checkout Code') {
            steps {
               echo "Pulling from GITHUB repository"
               git branch: 'main', credentialsId: 'srisanthosh26-ind', url: 'https://github.com/srisanthosh26-ind/devops.git'
            }
        }
         stage('Test the Project') {
            steps {
               echo "Test my JAVA project"
               bat 'mvn clean test' 
            }
              post {
                  always {
                         junit '**/target/surefire-reports/*.xml'
                         echo 'Test Run succeeded!'          
					}
				}
		}
        stage('Build Project') {
            steps {
               echo "Building my JAVA project"
               bat 'mvn clean package -DskipTests' 
            }
        }
        
        stage('Build the Docker Image') {
            steps {
               echo "Build the Docker Image"
               // Naming the image consistently for you
               bat 'docker build -t my-pipeline-app:v1 .'
            }
        }
        
        stage('Push Docker Image to DockerHub') {
            steps {
               echo "Push Docker Image to DockerHub"
               // Uses your 'dockerhubpwd' credential ID
               withCredentials([string(credentialsId: 'dockerhubpwd', variable: 'DOCKER_PASS')]) {
                    bat '''
                        echo %DOCKER_PASS% | docker login -u srisanthosh26 --password-stdin
                        docker tag my-pipeline-app:v1 srisanthosh26/my-pipeline-app:v1
                        docker push srisanthosh26/my-pipeline-app:v1
                    '''
               }
            }
        }
       
        stage('Deploy the project using Container') {
            steps {
                echo "Running Java Application"
                // This is the specific logic you requested:
                // 1. Remove any existing container named 'myjavaappcont' (ignore errors if it doesn't exist)
                // 2. Run the new container from the image we just pushed
                bat '''
                    docker rm -f myjavaappcont || exit 0
                    docker run --name myjavaappcont srisanthosh26/my-pipeline-app:v1
                '''
            }
        }
    }

    post {
        success {
            echo 'Pipeline Succeeded! Application is live.'
        }
        failure {
            echo 'Pipeline Failed. Check logs.'
        }
    }
}

