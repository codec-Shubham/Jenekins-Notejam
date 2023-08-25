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

                sh "docker build -t shubhamdev2001/notejam-application --no-cache Jenekins-Notejam/"

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
       stage("Start Config Maps, Secrets, Storage") {
    steps {
        script {
             kubeconfig = env.KUBECONFIG
            
            sh script: """
                kubectl --kubeconfig ${kubeconfig} apply -f /var/lib/jenkins/workspace/note-jenkins/Jenekins-Notejam/secret.yaml
                kubectl --kubeconfig ${kubeconfig} apply -f /var/lib/jenkins/workspace/note-jenkins/Jenekins-Notejam/config.yaml
                kubectl --kubeconfig ${kubeconfig} apply -f /var/lib/jenkins/workspace/note-jenkins/Jenekins-Notejam/storage.yaml
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
                kubectl --kubeconfig ${kubeconfig} apply -f /var/lib/jenkins/workspace/note-jenkins/Jenekins-Notejam/postgres-deploy.yml
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
                kubectl --kubeconfig ${kubeconfig} apply -f /var/lib/jenkins/workspace/note-jenkins/Jenekins-Notejam/notejam-application-deploy.yml
            """, 
            returnStatus: true
        }
    }
}

      stage("Minikube Service List and Get URL") {
    steps {
        script {
             serviceList
            withCredentials([string(credentialsId: 'kubernetes', variable: 'KUBECONFIG')]) {
                serviceList = sh(
                    script: """
                        kubectl --kubeconfig ${KUBECONFIG} get services -o json
                    """,  
                    returnStdout: true
                ).trim()
            }

            // Parse the JSON output using readJSON step
             servicesJson = readJSON(text: serviceList)
            
            // Iterate through the services
            servicesJson.items.each { service ->
                 serviceName = service.metadata.name
                 serviceType = service.spec.type
                 serviceIP = service.spec.clusterIP
                 servicePort = service.spec.ports[0].port
                
                if (serviceType == "NodePort") {
                     serviceURL = "http://${serviceIP}:${servicePort}"
                    echo "Service: ${serviceName}, URL: ${serviceURL}"
                    
                    // Here you can perform further actions with the serviceURL
                    // For example, make HTTP requests, trigger tests, etc.
                }
            }
        }
    }
}



    }

}

