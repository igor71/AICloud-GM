pipeline {
  agent {label 'gm-cloud-hub'}
    stages {
        stage('Build Docker Image ') {
            steps {
                sh '''#!/bin/bash -xe
                        CRED="server:123server123"
                        SRV=$(echo ${target_server} | awk -F [-] '{print $2}')
                        curl -u ${CRED} ftp://yifileserver/DOCKER_IMAGES/Tensorflow/Develop/map.csv -o map.csv
                        FTP_PATH=$(awk -F [,] -v srv="$SRV" '$6==srv' map.csv |  awk -F [,] -v python_version="$python_version" '$5==python_version' | awk -F [,] -v tf_version="$tensorflow_version" '$7~tf_version' | awk -F, '{print $4}')
                        FILE_NAME=$(echo $FTP_PATH | awk -F [/] '{print $7}')
	       		docker build --build-arg FILE_NAME=${FILE_NAME} --build-arg FTP_PATH=${FTP_PATH} -f Dockerfile.GM-tf-${python_version} -t gm-tf-${python_version}:${docker_tag} .
		            ''' 
            }
        }
	    stage('Test Docker Image') { 
            steps {
                sh '''#!/bin/bash -xe
		   echo 'Hello, Jenkins_Docker'
                    image_id="$(docker images -q gm-tf-${python_version}:${docker_tag})"
                      if [[ "$(docker images -q gm-tf-${python_version}:${docker_tag} 2> /dev/null)" == "$image_id" ]]; then
                          docker inspect --format='{{range $p, $conf := .RootFS.Layers}} {{$p}} {{end}}' $image_id
                      else
                          echo "It appears that current docker image corrapted!!!"
                          exit 1
                      fi 
                   ''' 
		    }
		}
		stage('Save & Load Docker Image') { 
            steps {
                sh '''#!/bin/bash -xe
		        echo 'Saving Docker image into tar archive'
                        docker save gm-tf-${python_version}:${docker_tag} | pv | cat > $WORKSPACE/gm-tf-${python_version}-${docker_tag}.tar
                        echo 'Remove Original Docker Image' 
		        CURRENT_ID=$(docker images | grep gm-tf-${python_version} | grep ${docker_tag} | awk '{print $3}')
			docker rmi -f gm-tf-${python_version}:${docker_tag}
                        
                        echo 'Loading Docker Image'
                        pv $WORKSPACE/gm-tf-${python_version}-${docker_tag}.tar | docker load
						docker tag $CURRENT_ID gm-tf-${python_version}:${docker_tag} 
                        
                        echo 'Removing temp archive.'  
                        rm $WORKSPACE/gm-tf-${python_version}-${docker_tag}.ta
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
