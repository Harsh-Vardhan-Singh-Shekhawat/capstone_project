pipeline {
    agent any

     environment{
       registryCredential = 'ecr:us-west-2:202051083'
       appRegistry = "203620234130.dkr.ecr.us-west-2.amazonaws.com/capstone_202051083"
       capstoneRegistry = "https://203620234130.dkr.ecr.us-west-2.amazonaws.com"
       cluster = "capstone_202051083"
        service = "<Service Name>"
   }

    stages {
        stage('Clone Website') {
            steps {
                git url:'https://github.com/Harsh-Vardhan-Singh-Shekhawat/capstone_project'
            }
        }

       stage("Docker Build Image"){
           steps{
             script{
                dockerImage = docker.build(appRegistry+ ":$BUILD_NUMBER",".")
             }
           }
      }

       stage('Test Website') {
            steps {
                // Run tests on the website
                sh 'echo "Running tests on the website"'
            }
       }

       stage("Upload App Image"){
         steps{
            script{
                docker.withRegistry(capstoneRegistry, registryCredential){
                    dockerImage.push("$BUILD_NUMBER")
                    dockerImage.push('latest')
                }
            }
         }
      }

      stage('Deploy to ecs'){
         when {
                branch "master"
         }
         steps{
            withAWS(credentials: '202051083', region: 'us-west-2'){
                sh 'aws ecs update-service --cluster ${cluster} --service ${service} --force-new-deployment'
            }
         }
      }

    }
}
