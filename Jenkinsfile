   pipeline {
      
            agent {
                docker {
                    image 'python:3-alpine'
                }
            }   

 
    
    stages {
        stage('Build') {

            steps {
                sh 'sudo pip install pytest'
                sh 'sudo pip install -r requirements.txt '
                sh 'sudo 2to3 -w sources/*.py'
                sh 'python -m py_compile  sources/*.py'
                stash(name: 'compiled-results', includes: 'sources/*.py*')
            }
        }
        stage('Test') {

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
