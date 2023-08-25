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

                sh "docker build -t fbdo/notejam --no-cache Notejam-pipeline/"

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
                kubectl --kubeconfig ${kubeconfig} apply -f /var/lib/jenkins/workspace/note-jenkins/Notejam-pipeline/secret.yaml
                kubectl --kubeconfig ${kubeconfig} apply -f /var/lib/jenkins/workspace/note-jenkins/Notejam-pipeline/config.yaml
                kubectl --kubeconfig ${kubeconfig} apply -f /var/lib/jenkins/workspace/note-jenkins/Notejam-pipeline/storage.yaml
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
                kubectl --kubeconfig ${kubeconfig} apply -f /var/lib/jenkins/workspace/note-jenkins/Notejam-pipeline/postgres-deploy.yml
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
                kubectl --kubeconfig ${kubeconfig} apply -f /var/lib/jenkins/workspace/note-jenkins/Notejam-pipeline/notejam-application-deploy.yml
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
