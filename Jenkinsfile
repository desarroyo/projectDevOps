pipeline{
    agent any
    options{
        timeout(time: 2, unit: 'MINUTES')
    }
    environment{
        ARTIFACT_ID = "desarroyo/app:${env.BUILD_NUMBER}"
    }
    stages {
        stage('Build') {
            steps {
                script {
                    dockerImage = docker.build "${env.ARTIFACT_ID}"
                }
            }
        }
        stage('Run test') {
            steps {
                sh "docker run ${env.ARTIFACT_ID} npm test"
            }
        }
        stage('Publish') {
            when {
                branch 'master'
            }
            steps {
                script {
                    docker.withRegistry("","DockerHubCredentials"){
                        dockerImage.push()
                    }
                }
            }
        }
        stage('Deployment') {
            when {
                branch 'master'
            }
            steps {
                buil job: 'deploy-app', parameters: [string(name: 'ARTIFACT_ID', value: "${env.ARTIFACT_ID}")], wait: false
            }
        }
    }
}