pipeline {
  agent {label 'gm-cloud-hub'}
    stages {
        stage('Build cloud-hub:0.0 Docker Image') {
            steps {
	       sh '''#!/bin/bash -xe
	       		TARGET_SRV=${target_server}
	       		docker build -f Dockerfile.CloudHub -t cloud-hub:0.0 .
		   '''  
            }
        }
	stage('Test The cloud-hub:0.0 Docker Image') { 
            steps {
                sh '''#!/bin/bash -xe
		    echo 'Hello, YI-TFLOW!!'
                    image_id="$(docker images -q cloud-hub:0.0)"
                      if [[ "$(docker images -q cloud-hub:0.0 2> /dev/null)" == "$image_id" ]]; then
                          docker inspect --format='{{range $p, $conf := .RootFS.Layers}} {{$p}} {{end}}' $image_id
                      else
                          echo "It appears that current docker image corrapted!!!"
                          exit 1
                      fi 
                   ''' 
            }
        }
        stage('Build The gm-tf-2.7:0.1  Docker Image ') {
            steps {
	       sh 'docker build -f Dockerfile.GM-tf-2.7 -t gm-tf-2.7:0.1 .'  
            }
        }
	stage('Test The gm-tf-2.7:0.1 Docker Image') { 
            steps {
                sh '''#!/bin/bash -xe
		   echo 'Hello, Jenkins_Docker'
                    image_id="$(docker images -q gm-tf-2.7:0.1)"
                      if [[ "$(docker images -q gm-tf-2.7:0.1 2> /dev/null)" == "$image_id" ]]; then
                          docker inspect --format='{{range $p, $conf := .RootFS.Layers}} {{$p}} {{end}}' $image_id
                      else
                          echo "It appears that current docker image corrapted!!!"
                          exit 1
                      fi 
                   ''' 
		    }
		}
 }
	post {
            always {
               script {
                  if (currentBuild.result == null) {
                     currentBuild.result = 'SUCCESS' 
                  }
               }
               step([$class: 'Mailer',
                     notifyEveryUnstableBuild: true,
                     recipients: "igor.rabkin@xiaoyi.com",
                     sendToIndividuals: true])
            }
         } 
}
