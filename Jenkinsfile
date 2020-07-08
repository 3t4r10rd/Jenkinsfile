pipeline {
    environment {
    registry = "mrbnd/repo"
    registryCredential = 'dockerhub'
    dockerImage = ''
  }

  agent {

    docker {
      image 'mrbnd/repo:build'
      args '-u root --privileged -v /var/run/docker.sock:/var/run/docker.sock'
    }

  }

  stages {

    stage('Git') {
      steps {
        git 'https://github.com/boxfuse/boxfuse-sample-java-war-hello.git'

      }
    }

    stage('Build') {
          steps {
            sh 'mvn package'
          }
        }

    stage('Make docker image') {
              steps {

                  sh 'cp /Dockerfile/Dockerfile .'
                  script {
                  dockerImage = docker.build registry + ":deploy"
                  }

              }
            }

    stage('Deploy Image') {
      steps{
        script {
          docker.withRegistry( '', registryCredential ) {
            dockerImage.push()
          }
        }
      }
    }

    stage('Run docker image on remote host') {
              steps {
                sh 'ssh-keyscan -H 84.201.139.111 >> ~/.ssh/known_hosts'
                sh '''ssh root@84.201.139.111 << EOF
	                docker run -it -d -p 80:8080 mrbnd/repo:deploy
                    '''

              }
    }
  }
}
