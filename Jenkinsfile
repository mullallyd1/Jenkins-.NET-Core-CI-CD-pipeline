pipeline {
    agent any
     triggers {
        githubPush()
      }
    stages {
        stage('Restore packages'){
           steps{
               sh 'dotnet restore WebApplication.sln'
            }
         }        
        stage('Clean'){
           steps{
               sh 'dotnet clean WebApplication.sln --configuration Release'
            }
         }
        stage('Build'){
           steps{
               sh 'dotnet build WebApplication.sln --configuration Release --no-restore'
            }
         }
        stage('Test: Unit Test'){
           steps {
                sh 'dotnet test XUnitTestProject/XUnitTestProject.csproj --configuration Release --no-restore'
             }
          }
        stage('Publish'){
             steps{
               sh 'dotnet publish WebApplication/WebApplication.csproj --configuration Release --no-restore'
             }
        }
        stage('Deploy'){
             steps{
               sh '''for pid in $(lsof -t -i:9090); do
                       kill -9 $pid
               done'''
               sh 'cd WebApplication/bin/Release/netcoreapp3.1/publish/'
               sh 'nohup dotnet WebApplication.dll --urls="http://127.0.0.1:9090" --ip="127.0.0.1" --port=9090 --no-restore > /dev/null 2>&1 &'
             }
        }        
        stage('Prep For Contrast Scan'){
             steps{
               sh 'rm -f WebApplication.zip'
               sh 'cd WebApplication/bin ; zip -r WebApplication.zip . ; cp -f WebApplication.zip ../../'
             }
        }   
        stage('Contrast Scan'){
             steps{
               sh 'contrast-cli  --scan WebApplication.zip --api_key "tN068mjN8xtZPyIHB5jXPg8rm1hRDxxE" --authorization "ZGF2aWQubXVsbGFsbHlAY29udHJhc3RzZWN1cml0eS5jb206OU1PTDNPWlpWUEg1MUxLSw==" --organization_id "c5f79c46-7c74-45a0-b4c4-565da52827fa" --host "teamserver-staging.contsec.com" --project_name DavidMNetCorePipeline1 --language DOTNET --wait_for_scan'
             }
        }
    }
}
