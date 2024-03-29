pipeline {
    agent any

    stages {
        stage('Compile') {
            steps {
                sh 'chmod +x ./gradlew && ./gradlew build'
            }
        }
        stage('Unit test') {
            steps {
                sh './gradlew test --info'
            }
        }

        stage('Code coverage') {
            steps {
                sh './gradlew jacocoTestCoverageVerification'
                sh './gradlew jacocoTestReport'
                publishHTML(target: [
                    reportDir: 'build/reports/jacoco/test/html',
                    reportFiles: 'index.html',
                    reportName: 'JaCoCo Report'
                ])
            }
        }
        stage('Static code analysis') {
            steps {
                sh './gradlew checkstyleMain'
                publishHTML(target: [
                    reportDir: 'build/reports/checkstyle/',
                    reportFiles: 'main.html',
                    reportName: 'CheckStyle Report'
                ])
            }
        }
        stage('Docker build') {
            steps {
                sh 'docker build -t 172.17.0.1:5000/calcjenkins .'
            }
        }
        stage('Docker push') {
            steps {
                sh 'docker push 172.17.0.1:5000/calcjenkins'
            }
        }
        stage('Deploy to staging') {
            steps {
                sh 'docker run -d --rm -p 172.17.0.1:8765:8080 --name calcjenkins 172.17.0.1:5000/calcjenkins'
            }
        }
        stage('Acceptance test') {
            steps {
                sleep 60
                sh 'chmod +x acceptance_test.sh && ./acceptance_test.sh'
            }
        }
    }
    post {
        always {
            sh 'docker stop calcjenkins'
        }
    }
}
