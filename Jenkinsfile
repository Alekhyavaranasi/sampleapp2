// Uses Declarative syntax to run commands inside a container.
pipeline {
    agent {
        kubernetes {
            // Rather than inline YAML, in a multibranch Pipeline you could use: yamlFile 'jenkins-pod.yaml'
            // Or, to avoid YAML:
            // containerTemplate {
            //     name 'shell'
            //     image 'ubuntu'
            //     command 'sleep'
            //     args 'infinity'
            // }
            yaml '''
apiVersion: v1
kind: Pod
spec:
  containers:
  - name: shell
    image: anandsadhu/dotnet-jenkins-slave
    command:
    - sleep
    args:
    - infinity
    tty: true
    resources:
      requests:
        memory: "2Gi"
    volumeMounts:
     - name: docker-sock
       mountPath: /var/run/docker.sock
  volumes:
    - name: docker-sock
      hostPath:
        path: /var/run/docker.sock
'''
            // Can also wrap individual steps:
            // container('shell') {
            //     sh 'hostname'
            // }
            defaultContainer 'shell'
        }
    }
    stages {
        stage('docker build') {
            steps {
                sh 'docker build -t alekhya277/sampleapp:latest .'
            }
        }
        stage('docker login') {
       steps {
           withCredentials([file(credentialsId: 'secret', variable: 'docker')]) {
               sh 'use ${docker}'
  }
        }
    }
    stage('docker push') {
        steps {
            sh 'docker push alekhya277/sampleapp'
           }
    }
        stage("install helm"){
       steps {
         sh 'curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3'
         sh 'chmod 700 get_helm.sh'
         sh './get_helm.sh'
           }
        }
       stage('helm') {
           steps {
               sh 'helm version'
               sh 'helm upgrade --install dotnet1 web-app'
            }
        }
    }
}
