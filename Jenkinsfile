pipeline {
    agent {
        label 'AWS_AGENT'
    }
    environment {
        AWS_ACCESS_KEY_ID = credentials('AWS_ACCESS_KEY')
        AWS_SECRET_ACCESS_KEY = credentials('AWS_PRIVATE_KEY')
    }
    stages {
            stage('checkout') {
              steps {
                 script{
                        dir("terraform")
                        {
                            git "https://github.com/jacksonlobo25/terrform-ec2.git"
                        }
                    }
                }
            }
 
        stage('validate') {
            steps {
                script {
                    sh 'terraform --version'
                    sh 'terraform init -backend-config="tfstate.config"'
                    sh 'terraform validate'
                }
            }
        }
       stage('Plan') {
            steps {
                sh 'pwd;cd terraform/ ; terraform init'
                sh "pwd;cd terraform/ ; terraform plan -out tfplan"
                sh 'pwd;cd terraform/ ; terraform show -no-color tfplan > tfplan.txt'
                }
                post {
                    success {
                        archiveArtifacts 'planfile'
                    }
                }
            }
        stage('apply') {
            when {
                expression {
                    currentBuild.result == null || currentBuild.result == 'SUCCESS'
                }
            }
            steps {
                input 'Proceed with Terraform apply?'
                script {
                    sh 'terraform apply -input=false planfile'
                }
            }
        }
        stage('destroy') {
            when {
                expression {
                    currentBuild.result == null || currentBuild.result == 'SUCCESS'
                }
            }
            steps {
                input 'Proceed with Terraform destroy?'
                script {
                    sh 'terraform destroy --auto-approve'
                }
            }
        }
    }
}
