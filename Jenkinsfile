pipeline {

    agent any
    tools {
        // Use the name of the Git installation you configured
        git 'MyGit'
    }


    environment {

        DOCKERHUB_CREDENTIALS = credentials('DOCKERHUB')
        KUBECONFIG = credentials('kube')
    }


    stages {


        stage("Clean-up") {

            steps {

                deleteDir()

            }

        }


        stage("Clone repo") {

            steps {

                sh "git clone https://github.com/codec-Shubham/Jenekins-Notejam.git"

            }

        }


        stage("Log-in") {

            steps {

                sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'

            }

        }


        stage("Build") {

            steps {

                sh "docker build -t shubhamdev2001/notejam-application Jenekins-Notejam/"

            }

        }
        stage("Push-repo") {

            steps {

                sh "docker push shubhamdev2001/notejam-application"

            }
        }
      
       stage("Start Config Maps, Secrets, Storage") {
    steps {
        script {
             kubeconfig = env.KUBECONFIG
            
            sh script: """
                kubectl --kubeconfig ${kubeconfig} apply -f /var/lib/jenkins/workspace/Notejam/secret.yaml
                kubectl --kubeconfig ${kubeconfig} apply -f /var/lib/jenkins/workspace/Notejam/config.yaml
                kubectl --kubeconfig ${kubeconfig} apply -f /var/lib/jenkins/workspace/Notejam/storage.yaml
            """, 
            returnStatus: true
        }
    }
}

       stage("Start Database") {
    steps {
        script {
             kubeconfig = env.KUBECONFIG
            
            sh script: """
                kubectl --kubeconfig ${kubeconfig} apply -f /var/lib/jenkins/workspace/Notejam/postgres-deploy.yml
            """, 
            returnStatus: true
        }
    }
}
        stage("Start Application") {
    steps {
        script {
             kubeconfig = env.KUBECONFIG
            
            sh script: """
                kubectl --kubeconfig ${kubeconfig} apply -f /var/lib/jenkins/workspace/Notejam/notejam-application-deploy.yml
            """, 
            returnStatus: true
        }
    }
}

       stage("Minikube Service List") {
    steps {
        script {
             kubeconfig = env.KUBECONFIG
            
            sh "kubectl --kubeconfig ${kubeconfig} get services"
        }
    }
}


    }

}
