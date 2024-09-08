pipeline {
    agent any

    options{
        ansiColor('xterm')
    }

    parameter{
        choice(name: 'ENV', choices: ['dev', 'prod'], description: 'Pick environment')
        choice(name: 'APP_NAME', choices: ['frontend', 'backend'], description: 'Choose APPLICATION')
        string(name: 'app_version', defaultValue: '', description: 'choose version to deploy')
    }
    stages{
        stage('Update Parameter Store'){
            steps{
                sh 'aws ssm put-parameter --name "${ENV}.${APP_NAME}.app_version" --type "String" --value "${app_version}" --overwrite'
            }
        }
        stage('Deployment'){
            steps{
                sh '''
                aws ec2 describe-instances --filters "Name=tag:Name,Values=${ENV}-${APP_NAME}" --query 'Reservations[*].Instances[*].PrivateIpAddress' --output text > host
                ansible-playbook -i host main.yml -e role_name=${APP_NAME} -e env=${app_version}
                '''
            }
        }
    }
}



aws ssm put-parameter --name "${ENV}.${APP_NAME}.app_version" --type "String" --value "${app_version}" --overwrite

aws ec2 describe-instances --filters "Values=elk"