    pipeline {
    agent none
    options {
        skipStagesAfterUnstable()
    }
    stages {
        stage('Build') {
            agent {
                docker {
                    image 'python:2-alpine'
                }
            }
            steps {
                sh 'pip install -r requirements.txt'
                sh 'python -m py_compile  sources/*.py'
                stash(name: 'compiled-results', includes: 'sources/*.py*')
            }
        }
        stage('Test') {
            agent {
                docker {
                    image 'qnib/pytest'
                }
            }
            steps {
                sh 'py.test --junit-xml test-reports/results.xml Tests/*.py'
            }
            post {
                always {
                    junit 'test-reports/results.xml'
                }
            }
        }
     stage('Packaging') {
            agent {
                docker {
                    image 'python:2-alpine'
                }
            }
               environment {
                   VOLUME = '$PWD/sources:/src'
                   IMAGE = 'cdrx/pyinstaller-linux:python3'
               }
           
            steps {
                dir(path: env.BUILD_ID) { 
                    unstash(name: 'compiled-results') 

                }
            }
            post {
                success {
                    
                    sh "ls" 
                    
                    archiveArtifacts '${env.BUILD_ID}/' 

                }
            }
        }
    }
}
