    pipeline {
    agent none
    //options {
      //  skipStagesAfterUnstable()
    //}
    stages {
        stage('Build') {
            agent {
                docker {
                    image 'python:2-alpine'
                }
            }
            steps {
                sh 'python -m py_compile sources/add2vals.py sources/calc.py'
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
                sh 'py.test --junit-xml test-reports/results.xml sources/test_calc.py'
            }
            post {
                always {
                    junit 'test-reports/results.xml'
                }
            }
        }
     stage('Packaging') {
           agent any
               environment {
                   VOLUME = '$PWD/sources:/src'
                   IMAGE = 'cdrx/pyinstaller-linux:python3'
               }
               steps {
                   dir(path: env.BUILD_ID) {
                       unstash(name: 'compiled-results')
                       //unstash(name: 'setUpPy')
                      // unstash(name: 'pypirc')
                      
                       sh 'cd sources'
                        sh 'ls -l'
                        sh 'python3 /*.py bdist_dumb --format=zip'
                        sh 'python3 /*.py sdist bdist_wheel'
                        sh 'python3 -m twine upload -r nexus-pypi dist/* --config-file .pypirc --verbose'
                    }
               }
               post {
                   success {
                        archiveArtifacts "${env.BUILD_ID}/dist/*"
                   }
               }
        }
    }
}
