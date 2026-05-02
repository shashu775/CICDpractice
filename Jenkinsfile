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
               bat "docker tag mynginx:latest 398934907029.dkr.ecr.us-east-1.amazonaws.com/my-nginx-app:latest"
               bat "docker tag mynginx:latest 398934907029.dkr.ecr.us-east-1.amazonaws.com/my-nginx-app:${env.IMAGE_TAG}
            }
        }
        stage ('Push Docker image'){
            steps{
               bat "docker push 398934907029.dkr.ecr.us-east-1.amazonaws.com/my-nginx-app:latest"
               bat "docker push 398934907029.dkr.ecr.us-east-1.amazonaws.com/my-nginx-app:${env.IMAGE_TAG}"
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
            // 1. Get and Clean the JSON
            // We use 'aws.exe' and 'foreach-object' to avoid the 'CantActivateDocumentInPipeline' error
            bat """
            powershell -Command "\$task = aws.exe ecs describe-task-definition --task-definition shashwat-web --query taskDefinition --output json | ConvertFrom-Json; \$task.PSObject.Properties.Remove('taskDefinitionArn'); \$task.PSObject.Properties.Remove('revision'); \$task.PSObject.Properties.Remove('status'); \$task.PSObject.Properties.Remove('requiresAttributes'); \$task.PSObject.Properties.Remove('compatibilities'); \$task.PSObject.Properties.Remove('registeredAt'); \$task.PSObject.Properties.Remove('registeredBy'); \$task | ConvertTo-Json -Depth 20 | Out-File -FilePath raw-task.json -Encoding ascii"
            """

            // 2. Inject the New Image Tag
            def newImage = "398934907029.dkr.ecr.us-east-1.amazonaws.com/my-nginx-app:${env.IMAGE_TAG}"
            bat """
            powershell -Command "\$json = Get-Content raw-task.json | ConvertFrom-Json; \$json.containerDefinitions[0].image = '${newImage}'; \$json | ConvertTo-Json -Depth 20 | Out-File -FilePath new-task-def.json -Encoding ascii"
            """

            // 3. Register the new version
            bat 'aws.exe ecs register-task-definition --region us-east-1 --cli-input-json file://new-task-def.json'
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
