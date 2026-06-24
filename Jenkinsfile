pipeline {
    agent {
        docker
        { 
            image 'postman/newman:latest'
            args '-u=root --entrypoint='
        }  
    }
    parameters{
        //collection1 choix
        booleanParam(name: 'firstCollection', defaultValue: true, description: 'Toggle this to test the first collection')
        //Choix d'environnements
        choice(name: 'envChoice', choices: ['env1_jenkins', 'env2_jenkins', 'env3_jenkins'], description: 'Pick the environment to launch the tests with')
    }

    stages {
        stage('lancer le test') {
            steps {
                script{
                    if(params.firstCollection){
                        sh "newman run collections/collection.json";
                    }
                    else{
                        switch(params.envChoice){
                            case("env1_jenkins"):
                                sh "newman run collections/collection_1_jenkins.json -e environments/env1_jenkins";
                                break;
                            case("env2_jenkins"):
                                sh "newman run collections/collection_1_jenkins.json -e environments/env2_jenkins";
                                break;
                            case("env3_jenkins"):
                                sh "newman run collections/collection_2_jenkins.json -e environments/env3_jenkins";
                                break;
                            default:
                                sh "newman run collections/collection.json";
                                sh "newman run collections/collection_1_jenkins.json -e environments/env1_jenkins";
                                sh "newman run collections/collection_2_jenkins.json -e environments/env3_jenkins";
                                break;
                        }
                    }
                }
            }
        }
    }
}
