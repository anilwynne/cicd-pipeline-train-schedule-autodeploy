pipeline {
    agent any
    stages {
        /*stage('compile') {
            steps {
                sh 'mvn -B compile'
            }
        }
        stage('test') {
            steps {
                sh 'mvn -B test'
            }
        }
        stage('package') {
            steps {
                sh 'mvn -B package'
            }
        }*/
        stage('Build') {
            steps {
               echo 'Running build automation'
               sh 'sudo ./gradlew build --no-daemon'
               archiveArtifacts artifacts: 'dist/trainSchedule.zip'
            }
        }
         stage ('build and push docker image') {
            steps {
                sh 'sudo docker build -t anilwynne/devopsproject2:$BUILD_NUMBER .'
                withCredentials([usernamePassword(credentialsId: 'dockerhub', passwordVariable: 'pass', usernameVariable: 'user')]) {
                    sh "sudo docker login -u ${env.user} -p ${env.pass}"
                    sh 'sudo docker push devopsxprts/addressbook:$BUILD_NUMBER'
                }
            }
        }
        stage('CanaryDeploy') {
            //when {
                //branch 'master'
           // }
            environment { 
                CANARY_REPLICAS = 1
            }
            steps {
                kubernetesDeploy(
                    kubeconfigId: 'kubeconfig',
                    configs: 'train-schedule-kube-canary.yml',
                    enableConfigSubstitution: true
                )
            }
        }
        stage('DeployToProduction') {
           // when {
                //branch 'master'
            //}
            environment { 
                CANARY_REPLICAS = 0
            }
            steps {
                input 'Deploy to Production?'
                milestone(1)
                kubernetesDeploy(
                    kubeconfigId: 'kubeconfig',
                    configs: 'train-schedule-kube-canary.yml',
                    enableConfigSubstitution: true
                )
                kubernetesDeploy(
                    kubeconfigId: 'kubeconfig',
                    configs: 'train-schedule-kube.yml',
                    enableConfigSubstitution: true
                )
            }
        }
    }
}
