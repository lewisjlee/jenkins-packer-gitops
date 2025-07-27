pipeline {
    agent any
    environment {
        AWS_REGION = 'ap-northeast-2'
    }
    stages {
        stage('Switch to S3 Backend & Deploy App Instance') {
            steps {
                sh '''
                # S3 버킷 이름 추출
                S3_BUCKET=$(aws s3 ls --region $AWS_REGION | grep terraform-state | tail -n1 | awk '{print $3}')
                # backend.tf의 주석 해제 및 버킷명 치환
                sed -i "s/TERRAFORM-STATE-BUCKET/${S3_BUCKET}/" backend.tf
                sed -i 's/#//g' backend.tf
                # amivar.tf 동기화
                aws s3 cp s3://${S3_BUCKET}/amivar.tf amivar.tf --region $AWS_REGION
                terraform init
                terraform apply -auto-approve -var APP_INSTANCE_COUNT=1 -target=aws_instance.app-instance
                '''
            }
        }
    }
} 