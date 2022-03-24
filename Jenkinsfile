properties([pipelineTriggers([githubPush()])])

pipeline {
    environment {
        // name of the image without tag
        dockerRepo = "juno2/edu1"
        dockerCredentials = 'docker_ci'
        dockerImageVersioned = ""
        dockerImageLatest = ""
    }

    agent any

    stages {
        /* checkout repo */
        stage('Checkout SCM') {
            steps {
                checkout([
                 $class: 'GitSCM',
                 //branches: [[name: '*/master']],
                 //branches: [[name: 'refs/tags/${TAG}']],
                 userRemoteConfigs: [[
                    url: 'https://github.com/yunvo/edu1.git',
                    credentialsId: 'github_ci', 
                 ]]
                ])
            }
        }
        stage("Building docker image"){
            steps{
                script{
                    dockerImageVersioned = docker.build dockerRepo //+ ":$BUILD_NUMBER"
                    dockerImageLatest = docker.build dockerRepo + ":latest"
                }
            }
        }
        stage("Pushing image to registry"){
            steps{
                script{
                    // if you want to use custom registry, use the first argument, which is blank in this case
                    docker.withRegistry( '', dockerCredentials){
                        dockerImageVersioned.push()
                        dockerImageLatest.push()
                    }
                }
            }
        }
        stage('Cleaning up') {
            steps {
                sh "docker rmi $dockerRepo"//:$BUILD_NUMBER"
            }
        }
    }

    /* Cleanup workspace */
    post {
       always {
           deleteDir()
       }
   }
}
