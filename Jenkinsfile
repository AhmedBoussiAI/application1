   pipeline {
    agent  none    

    options {
        skipStagesAfterUnstable()
    }
    stages {
        stage('Build') {
            agent {
                docker {
                    image 'python:3-alpine'
                }
            }
            steps {
                sh 'pip install -r requirements.txt'
                sh '2to3 -w sources/*.py'
                sh 'python -m py_compile  sources/*.py'
                stash(name: 'compiled-results', includes: 'sources/*.py*')
            }
        }
        stage('Test') {
            agent {
                docker {
                    image 'grihabor/pytest'

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
                    image 'python:3-alpine'
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
                    
                    echo '$BUILD_ID'
                     


                }
            }
        }
      stage('Archive') {
                      agent {
                docker {
                    image 'python:3-alpine'
                }
            }
                      steps {
                             sh 'tar -cvzf sources.tar.gz --strip-components=1 sources'
                             archive 'sources.tar.gz'
                             }
        
    } 
            stage('Deploy') {
                steps {
                     
                        echo 'Deploying...'
    }
                
    }
}
    } 
