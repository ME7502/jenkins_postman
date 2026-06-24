pipeline {
    agent {
        docker { 
            // image 'postman/newman:latest'
            image 'node:latest'
            args '-u=root --entrypoint='
        }  
    }

    parameters {
        booleanParam(
            name: 'firstCollection',
            defaultValue: true,
            description: 'Toggle this to test the first collection'
        )

        choice(
            name: 'envChoice',
            choices: ['env1_jenkins', 'env2_jenkins', 'env3_jenkins','tout'],
            description: 'Pick the environment to launch the tests with'
        )
    }

    stages {

        stage('lancer le test') {
            steps {
                sh "npm install -g newman newman-reporter-allure allure-commandline"
                script {

                    if (params.firstCollection) {
                        sh "newman run collections/collection.json -n 2 -r cli,allure --reporter-allure-export allure-results && allure generate allure-results -o allure-report --clean"
                    } else {

                        switch(params.envChoice) {

                            case("env1_jenkins"):
                                sh "newman run collections/collection_1_jenkins.json -e environments/env1_jenkins.json"
                                break

                            case("env2_jenkins"):
                                sh "newman run collections/collection_1_jenkins.json -e environments/env2_jenkins.json"
                                break

                            case("env3_jenkins"):
                                sh "newman run collections/collection_2_jenkins.json -e environments/env3_jenkins.json"
                                break

                            default:
                                sh "newman run collections/collection.json"
                                sh "newman run collections/collection_1_jenkins.json -e environments/env1_jenkins.json"
                                sh "newman run collections/collection_2_jenkins.json -e environments/env3_jenkins.json"
                                break
                        }
                    }
                }
            }
        }
    }

    post {
        always {
            archiveArtifacts artifacts: 'allure-report/**', fingerprint: true
        }
    }
}