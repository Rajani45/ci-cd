pipeline{
    agent any
    tools
    {
        maven 'maven'
    }
    stages{
        stage('checkout from scm')
        {
            steps{
                checkout scmGit(branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/Rajani45/ci-cd.git']])
            }
        }
        stage('build')
        {
            steps{
                sh 'mvn clean install package'
            }
        }
        stage ('docker image build')
        {
            steps{
                sh 'docker build -t rajaninandu/cicd-image .'
            }
        }
        stage('pusing image')
        {
            steps{
                script{
                withCredentials([string(credentialsId: 'dockerhubpwd', variable: 'dockerhubpwd')]) {
                    sh 'docker login -u rajaninandu -p ${dockerhubpwd}'
                }
                    sh 'docker push rajaninandu/cicd-image'
                }
            }
        }
        stage('deploying in k8s')
        {
            steps{
                withKubeConfig(caCertificate: '', clusterName: '', contextName: '', credentialsId: 'k8s', namespace: '', restrictKubeConfigAccess: false, serverUrl: '') {
                    sh 'kubectl apply -f deploymentservice.yaml'

                # sshagent(['k8s-ssh']) {
                # sh "scp -o StrictHostKeyChecking=no deploymentservice.yaml ubuntu@52.23.252.160:/home/ubuntu"
                # script{
                # //try{
                # sh "ssh  ubuntu@52.23.252.160 kubectl apply -f ."
                # }
                # //sh " ubuntu@52.23.252.160 kubectl create -f ."
                # }
                # }
                # }    
                }
            }
        }
    }
}
