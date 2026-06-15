pipeline{
    agent any
    
    environment {
        AWS_DEFAULT_REGION='us-east-1'
        S3_BUCKET='learndemobuck'
        DISTRIBUTION_ID=''
    }
    stages{
        stage('clone repositry') {
            steps {
                git credentialsId: 'kreethiwas06'
                url: 'https://github.com/kreethiwas06/sample-devops-frontend-project.git'
                
            }
        }
        stage ('install&Build') {
            steps {
                sh'''
                rm -rf mode-modules package-lock.json
                npm install --legacy-peer-deps || true
                npm run build
                '''
            }
        }
        stage ('deploy to s3') {
            steps {withCredentials([aws(accessKeyVariable: 'AWS_ACCESS_KEY_ID', credentialsId: 'awscred', secretKeyVariable: 'AWS_SECRET_ACCESS_KEY')])
            
            sh '''
            aws s3 sync ./ dist s3 ://$s3  --bucket delete
            '''
                
            }
        }
        stage ('inalidate cloudfront') {
            steps {
                withCredentials([aws(accessKeyVariable: 'AWS_ACCESS_KEY_ID', credentialsId: 'awscred', secretKeyVariable: 'AWS_SECRET_ACCESS_KEY')]) {
                    sh '''
                    aws cloudfront create-invalidation --distribution-id YOUR_DISTRIBUTION_ID --paths "/*"
                    '''
                }
            }
        }
    }
}
