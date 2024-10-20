pipeline {
    agent { node { label "Sonarqube_Instance" } }   
    parameters {
      choice(name: 'Environment', choices: ['Dev', 'QA', 'UAT', 'Prod'], description: 'Target environment for deployment')
      string(name: 'ecr_tag', defaultValue: '1.0.0', description: 'Assign the ECR tag version for the build')
    }

    tools {
      maven "Maven-3.9.9"
    }

    stages {
    stage('1. Git Checkout') {
      steps {
        git branch: 'release', credentialsId: 'github-jenkins-auth', url: 'https://github.com/dbesong/addressbook'
      }
    }
    //Github-pat
    stage('2. Build with Maven') { 
      steps {
        sh "mvn clean package"
      }
    }
    //stage('3. SonarQube Analysis') {
        //  environment {
          //      scannerHome = tool 'SonarQube-Scanner-6.2.1'
          //  }
         //   steps {
          //    withCredentials([string(credentialsId: 'sonarqube-token', variable: 'SONAR_TOKEN')]) {
            //          sh """
            //          ${scannerHome}/bin/sonar-scanner  
            //          -Dsonar.projectKey=addressbook                   
            //          -Dsonar.projectName='addressbook'                             
             //         -Dsonar.host.url=http://44.210.143.67:9000                    
             //         -Dsonar.token=${SONAR_TOKEN} 
              //        -Dsonar.sources=src/main/java/ 
               //       -Dsonar.java.binaries=target/classes 
             //        """
            //      }
           //   }
       // }

//addressbook-application \
//'addressbook-application' \
//https://sonarqube.dominionsystem.org \

// mvn clean verify sonar:sonar \
//   -Dsonar.projectKey=addressbook \
//   -Dsonar.projectName='addressbook' \
//   -Dsonar.host.url=http://184.72.109.208:9000 \
//   -Dsonar.token=sqp_78ea52967efb7a8c67b2356b386a782af9c6444d









    stage('4. Docker Image Build') {
      steps {
        //sh "aws ecr get-login-password --region us-east-1 | sudo docker login --username AWS --password-stdin 654654439077.dkr.ecr.us-east-1.amazonaws.com"
        sh "aws ecr get-login-password --region us-east-1 | sudo docker login --username AWS --password-stdin 654654439077.dkr.ecr.us-east-1.amazonaws.com"
        sh "sudo docker build -t addressbook ."
        sh "sudo docker tag addressbook:latest 654654439077.dkr.ecr.us-east-1.amazonaws.com/addressbook:${params.ecr_tag}"
        sh "sudo docker push 654654439077.dkr.ecr.us-east-1.amazonaws.com/addressbook:${params.ecr_tag}"
      }
    }

//654654439077.dkr.ecr.us-east-1.amazonaws.com/addressbook



      //steps {
        //sh "aws ecr-public get-login-password --region us-east-1 | sudo docker login --username AWS --password-stdin public.ecr.aws/a1o0c8b5"
        //sh "sudo docker build -t addressbook ."
        //sh "sudo docker tag addressbook:latest public.ecr.aws/a1o0c8b5/addressbook:${params.ecr_tag}"
        //sh "sudo docker push public.ecr.aws/a1o0c8b5/addressbook:${params.ecr_tag}"
      //}
    //}

    stage('5. Application Deployment in EKS') {
      steps {
        kubeconfig(caCertificate: '', credentialsId: 'kubeconfig', serverUrl: '') {
          sh "kubectl apply -f manifest"
        }
      }
    }

    stage('6. Monitoring Solution Deployment in EKS') {
      steps {
        kubeconfig(caCertificate: '', credentialsId: 'kubeconfig', serverUrl: '') {
          sh "chmod +x install_prometheus.sh"
          sh "kubectl apply -k monitoring"
          sh("""script/install_helm.sh""") 
          sh("""script/install_prometheus.sh""") 
        }
      }
    }

    stage('7. Email Notification') {
      steps {
        mail bcc: 'fusisoft@gmail.com', body: '''Build is Over. Check the application using the URL below:
         https://abook.dominionsystem.com/addressbook-1.0
         Let me know if the changes look okay.
         Thanks,
         Dominion System Technologies,
         +1 (313) 413-1477''', 
         subject: 'Application was Successfully Deployed!!', to: 'fusisoft@gmail.com'
      }
    }
  }
}





