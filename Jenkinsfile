   pipeline {
      
            agent any

 
    
    stages {
        stage('Build') {

            steps {
                sh ' pip3 install pytest'
                sh ' pip3 install -r requirements.txt '

                sh 'python3 -m py_compile  sources/*.py'
                stash(name: 'compiled-results', includes: 'sources/*.py*')
            }
        }
        stage('Test') {

            steps {
                sh 'python3 -m pytest --junit-xml test-reports/results.xml Tests/*.py'
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
