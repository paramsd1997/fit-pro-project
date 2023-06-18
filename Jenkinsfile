node {
  stage('SCM checkout') {
    git branch: 'main', url: 'https://github.com/shwetakumari2305/fit-pro-project.git'
  }
  stage('Mvn Package') {
    def mvnHome = tool name: 'maven1', type: 'maven'
    def mvnCMD = "${mvnHome}/bin/mvn"
    sh "${mvnCMD} clean package -Dmaven.test.skip=true"
  }
  stage('docker compose up') {
    sh 'docker-compose up -d'
  }
  stage('tagging the images') {
    sh 'docker tag fit-pro-master-emailservice:latest shwetashukla23/fit-pro-master-emailservice:latest'
    sh 'docker tag fit-pro-master-userservice:latest shwetashukla23/fit-pro-master-userservice:latest'
    sh 'docker tag fit-pro-master-paymentservice:latest shwetashukla23/fit-pro-master-paymentservice:latest'
    sh 'docker tag fit-pro-master-authenticationservice:latest shwetashukla23/fit-pro-master-authenticationservice:latest'
    sh 'docker tag product-webapp:latest shwetashukla23/product-webapp:latest'
    sh 'docker tag fit-pro-master-appointmentservice:latest shwetashukla23/fit-pro-master-appointmentservice:latest'
    sh 'docker tag fit-pro-master-api-gateway:latest shwetashukla23/fit-pro-master-api-gateway:latest'
    sh 'docker tag fit-pro-master-eurekaserver:latest shwetashukla23/fit-pro-master-eurekaserver:latest'
    sh 'docker tag fit-pro-master-configserver:latest shwetashukla23/fit-pro-master-configserver:latest'
  }

  stage('Push Docker Image') {
    withCredentials([string(credentialsId: 'dockerhub_password1', variable: 'dockerhubpwd')]) {
      sh "docker login -u shwetashukla23 -p ${dockerhubpwd}"
    }
    sh 'docker push shwetashukla23/fit-pro-master-emailservice:latest'
    sh 'docker push shwetashukla23/fit-pro-master-userservice:latest'
    sh 'docker push shwetashukla23/fit-pro-master-paymentservice:latest'
    sh 'docker push shwetashukla23/fit-pro-master-authenticationservice:latest'
    sh 'docker push shwetashukla23/product-webapp:latest'
    sh 'docker push shwetashukla23/fit-pro-master-appointmentservice:latest'
    sh 'docker push shwetashukla23/fit-pro-master-api-gateway:latest'
    sh 'docker push shwetashukla23/fit-pro-master-eurekaserver:latest'
    sh 'docker push shwetashukla23/fit-pro-master-configserver:latest'
  }
  stage('k8s deployment') {
    sshagent(['k8sMaster']) {
      script {
        def folderPath = 'k8s-fit-pro-new'
        def folderExistsOutput = sh(
          returnStdout: true,
          script: "ssh -o StrictHostKeyChecking=no -l ec2-user 172.31.23.244 test -d ${folderPath} && echo true || echo false"
        )
        def folderExists = folderExistsOutput.trim().toString()

        echo "Folder Exists Output: ${folderExistsOutput}"
        echo "Folder Exists: ${folderExists}"

        if (folderExists == 'true') {
          sh 'ssh -o StrictHostKeyChecking=no -l ec2-user 172.31.23.244 ls'
          sh 'ssh -o StrictHostKeyChecking=no -l ec2-user 172.31.23.244 kubectl create -f k8s-fit-pro-new/'
        } else {
          sh "ssh -o StrictHostKeyChecking=no -l ec2-user 172.31.23.244 git clone https://github.com/shwetakumari2305/k8s-fit-pro-new.git"
          sh 'ssh -o StrictHostKeyChecking=no -l ec2-user 172.31.23.244 kubectl create -f k8s-fit-pro-new/'
        }
      }
    }
  }
}
