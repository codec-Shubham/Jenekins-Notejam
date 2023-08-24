pipeline {

    agent any


    environment {

        DOCKERHUB_CREDENTIALS = credentials('DOCKERHUB')
        KUBECONFIG = credentials('kubernetes')
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

                sh "docker build -t shubhamdev2001/notejam-application --no-cache Notejam-pipeline/"

            }

        }
        stage("Push-repo") {

            steps {

                sh "docker push shubhamdev2001/notejam-application"

            }
        }
        // stage("Minikube Service Start") {

        //     steps {
              
        //               sh "minikube start"
                  
        //     }
        // }
        stage("Start Config Maps , Secrets ,Storage") {

            steps {
              
                      sh "kubectl --kubeconfig $KUBECONFIG apply -f /var/lib/jenkins/workspace/note-jenkins/Jenekins-Notejam/secret.yaml"
                      sh "kubectl --kubeconfig $KUBECONFIG apply -f /var/lib/jenkins/workspace/note-jenkins/Jenekins-Notejam/config.yaml"
                      sh "kubectl --kubeconfig $KUBECONFIG apply -f /var/lib/jenkins/workspace/note-jenkins/Jenekins-Notejam/storage.yaml"
                  
            }
        }
        stage("Start Database") {

            steps {
              
                      sh "kubectl --kubeconfig $KUBECONFIG apply -f /var/lib/jenkins/workspace/note-jenkins/Jenekins-Notejam/postgres-deploy.yml"
                  
            }
        }
         stage("Start Application") {

            steps {
              
                      sh "kubectl --kubeconfig $KUBECONFIG apply -f /var/lib/jenkins/workspace/note-jenkins/Jenekins-Notejam/notejam-application-deploy.yml"
                  
            }
        }
        stage("Minikube Service List") {

            steps {
              
                      sh "kubectl --kubeconfig $KUBECONFIG get services"
            }
        }

    }

}

