pipeline {
    agent { label 'docker'}
environment {
    IMAGE_TAG = "${BUILD_NUMBER}"
}    

stages {
        stage('Build Docker Image') {
            steps {
                bat 'cd three-tier-architecture-aws/frontend && docker build -t mynginx .'
            }
        }

        stage('Verify Image') {
            steps {
                bat 'docker images'
            }
        }
        stage('AWS login') {
            steps {
                bat 'aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 398934907029.dkr.ecr.us-east-1.amazonaws.com'
            }
        }
    
        stage('Tag Docker image'){
            steps{
                bat 'docker tag mynginx:latest 398934907029.dkr.ecr.us-east-1.amazonaws.com/my-nginx-app:latest'
            }
        }
        stage ('Push Docker image'){
            steps{
                bat ' docker push  398934907029.dkr.ecr.us-east-1.amazonaws.com/my-nginx-app:latest'
            }
        }
        //stage ('Update task revision'){
            //steps{
            //    bat 'aws ecs describe-task-definition --task-definition shashwat-web  --query taskDefinition --output json | jq "del(.taskDefinitionArn, .revision, .status, .requiresAttributes, .compatibilities, .registeredAt, .registeredBy)" > new-task-def.json'
          //      bat    'aws ecs register-task-definition  --region us-east-1 --cli-input-json file://new-task-def.json' 
        //         }
       // }
          stage ('Update task revision') {
    steps {
        script {
            // 1. Get the current definition and clean it using PowerShell (since jq is missing)
            bat """
            powershell -Command "(aws ecs describe-task-definition --task-definition shashwat-web --query taskDefinition --output json | ConvertFrom-Json) | Object { \$_.PSObject.Properties.Remove('taskDefinitionArn'); \$_.PSObject.Properties.Remove('revision'); \$_.PSObject.Properties.Remove('status'); \$_.PSObject.Properties.Remove('requiresAttributes'); \$_.PSObject.Properties.Remove('compatibilities'); \$_.PSObject.Properties.Remove('registeredAt'); \$_.PSObject.Properties.Remove('registeredBy'); return \$_ } | ConvertTo-Json -Depth 20 | Out-File -FilePath raw-task.json -Encoding ascii"
            """

            // 2. Define the new image string
            def newImage = "398934907029.dkr.ecr.us-east-1.amazonaws.com/my-nginx-app:${env.IMAGE_TAG}"

            // 3. Use PowerShell to find the image field and swap the value
            bat """
            powershell -Command "\$json = Get-Content raw-task.json | ConvertFrom-Json; \$json.containerDefinitions[0].image = '${newImage}'; \$json | ConvertTo-Json -Depth 20 | Out-File -FilePath new-task-def.json -Encoding ascii"
            """

            // 4. Register the new version
            bat 'aws ecs register-task-definition --region us-east-1 --cli-input-json file://new-task-def.json'
        }
    }
}
    
        stage ('Redeploy nginx task') {
            steps{
                bat 'aws ecs update-service --cluster shashwat_nginx --service shashwat-web-service-6eypc117 --task-definition shashwat-web --force-new-deployment --region us-east-1'
            }
        }

  } 
    
}
