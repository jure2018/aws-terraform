pipeline {
    agent {dockerfile true}
    options {
        withAWS(credentials:'1b21ca40-c9c8-4d68-92ac-edecf28c96c9')
    }
    parameters {
        choice(name: 'ACCOUNT', choices: ['308620678440', 'other'], description: 'Select the AWS account')
        string(name: 'WORKINGDIR', defaultValue: 's3', description: 'working directory for Terraform command')
        choice(name: 'COMMAND', choices: ['init', 'plan', 'apply'], description: 'Select Terraform command for this build')
    }
    stages {
        stage('terragrunt init') {
            when { expression { params.COMMAND == 'init' } }
            steps {
                dir(params.ACCOUNT) {
                    sh '''
                    cd $WORKINGDIR
                    terragrunt init -input=false
                    '''
                }
            }
        }
        stage('terragrunt plan') {
            when { expression { params.COMMAND == 'plan' } }
            steps {
                dir(params.ACCOUNT) {
                    sh '''
                    cd $WORKINGDIR
                    terragrunt plan -out=$WORKINGDIR.plan
                    '''
                }
            }
        }
        stage('terragrunt apply') {
            when { expression { params.COMMAND == 'apply' } }
            steps {
                dir(params.ACCOUNT) {
                    sh '''
                    cd $WORKINGDIR
                    terragrunt apply -input=false -auto-approve $WORKINGDIR.plan
                    '''
                }
            }
        }
        stage('clean up!') {
            when { expression { params.COMMAND == 'apply' } }
            steps {
                cleanWs cleanWhenAborted: false,
                cleanWhenFailure: false,
                cleanWhenNotBuilt: false,
                cleanWhenUnstable: false,
                notFailBuild: true
            }
        }
    } // stages
} // pipeline
