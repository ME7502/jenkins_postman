pipeline {
    agent {
        docker { 
            image 'node:20'
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
            choices: ['env1_jenkins', 'env2_jenkins', 'env3_jenkins', 'tout'],
            description: 'Pick the environment to launch the tests with'
        )
    }
    stages {
        stage('Clean Workspace') {
            steps {
                cleanWs()
            }
        }
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('Install Dependencies') {
            steps {
                sh "npm install -g newman newman-reporter-htmlextra"
            }
        }
        stage('Run Tests') {
            steps {
                script {
                    if (params.firstCollection) {
                        sh """
                            newman run collections/collection.json \
                                -n 2 \
                                -r htmlextra \
                                --reporter-htmlextra-export reports/report.html
                        """
                    } else {
                        switch(params.envChoice) {
                            case 'env1_jenkins':
                                sh """
                                    newman run collections/collection_1_jenkins.json \
                                        -e environments/env1_jenkins.json \
                                        -r htmlextra \
                                        --reporter-htmlextra-export reports/report_env1.html
                                """
                                break
                            case 'env2_jenkins':
                                sh """
                                    newman run collections/collection_1_jenkins.json \
                                        -e environments/env2_jenkins.json \
                                        -r htmlextra \
                                        --reporter-htmlextra-export reports/report_env2.html
                                """
                                break
                            case 'env3_jenkins':
                                sh """
                                    newman run collections/collection_2_jenkins.json \
                                        -e environments/env3_jenkins.json \
                                        -r htmlextra \
                                        --reporter-htmlextra-export reports/report_env3.html
                                """
                                break
                            default:
                                sh """
                                    newman run collections/collection.json \
                                        -r htmlextra \
                                        --reporter-htmlextra-export reports/report_all_1.html
                                    newman run collections/collection_1_jenkins.json \
                                        -e environments/env1_jenkins.json \
                                        -r htmlextra \
                                        --reporter-htmlextra-export reports/report_all_2.html
                                    newman run collections/collection_2_jenkins.json \
                                        -e environments/env3_jenkins.json \
                                        -r htmlextra \
                                        --reporter-htmlextra-export reports/report_all_3.html
                                """
                                break
                        }
                    }
                }
            }
        }
    }
    post {
        always {
            publishHTML(target: [
                allowMissing: false,
                alwaysLinkToLastBuild: true,
                keepAll: true,
                reportDir: 'reports',
                reportFiles: '*.html',
                reportName: 'Newman HTML Report'
            ])
            archiveArtifacts artifacts: 'reports/**/*.html', fingerprint: true
        }
    }
}