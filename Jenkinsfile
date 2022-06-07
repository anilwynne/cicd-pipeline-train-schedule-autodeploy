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
        //stage('package') {
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
                    sh 'sudo docker push anilwynne/devopsproject2:$BUILD_NUMBER'
                }
            }
        }
         stage ('kubernetes deployment') {
            steps {
               sh 'sudo kubectl apply -f train-schedule-kube.yml'
               //sh 'kubectl set image deployment  anilwynne/devopsproject2=anilwynne/devopsproject2:$BUILD_NUMBER'
                
            }
        }

    }
}
        
