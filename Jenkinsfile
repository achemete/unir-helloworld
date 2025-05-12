pipeline {
    agent any

    stages {
        stage('Get Code') {
            steps {
                echo 'Pulling the code from the repo'
                // Obtener código del repo
                git 'https://github.com/achemete/unir-helloworld.git'
                sh 'ls -la'
                echo "${env.WORKSPACE}"
                stash name: 'codigo', includes: '**/*'
            }
        }

        stage('Build') {
            steps {
                echo "No hay que compilar nada. Esto es python..."
            }
        }
        
        stage('Tests'){
            parallel {
                
                stage('Unit') {
                    steps {
                        unstash name: 'codigo'
                        sh '''
                        export PYTHONPATH=$WORKSPACE
                        pytest --junitxml=result-unit.xml test/unit
                        '''
                    }
                }
        
                stage('Rest') {
                    steps {
                        unstash name: 'codigo'
                        catchError(buildResult: 'UNSTABLE', stageResult: 'FAILURE'){
                            sh '''
                            export FLASK_APP=app/api.py
                            export FLASK_ENV=development
                            flask run &
            
                            java -jar /home/hector/unir-helloworld/test/wiremock/wiremock-standalone-3.13.0.jar \
                                --port 9000 \
                                --root-dir /home/hector/unir-helloworld/test/wiremock &
            
                            export PYTHONPATH=$WORKSPACE
                            '''
                        }
                    }
                }
            }
        }
        stage('Results'){
            steps {
                sh 'ls -la'
                sh 'pwd'
                sh 'sleep 5'
                junit '**/result-unit.xml'
            }
        }
    }
}
