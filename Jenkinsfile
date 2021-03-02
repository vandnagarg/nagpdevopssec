pipeline{
	agent any
	
	environment{
	    scannerhome = tool name: 'sonar_scanner_dotnet' , type:'hudson.plugins.sonar.MsBuildSQRunnerInstallation'
		branch='develop'
	}
	
	stages{
		stage('checkout'){
			steps{
				checkout scm
			}
		}
		stage("nuget"){
			steps{
				bat script:'dotnet restore'
			}
		}
		stage("test"){
			steps{
				bat script:'dotnet test'
			}
		}
		stage("start sonar"){
		    steps{
		        withSonarQubeEnv('Test_Sonar') {
                    echo "start sonar"
                    bat script : 'dotnet "%scannerhome%/sonarscanner.msbuild.dll" begin /k:dontet /n: dotnet /v:1.0'
                }
		    }
		}
		stage("dotnet build"){
		    steps{
		        bat script:'dotnet build'
		    }
		}
		stage("sonar end"){
		    steps{
		        withSonarQubeEnv('Test_Sonar') {
                    bat script : 'dotnet "%scannerhome%/sonarscanner.msbuild.dll" end'
                }
		    }
		}
		stage("docker build"){
		    steps{
		        echo "docker build"
		        bat script: 'docker build -t dtr.nagarro.com:443/nagp-dotnet-vandnagarg:%BUILD_NUMBER% ./Nagpdotnet'
		    }
		}
		stage("delete container develop"){
			when{
				brach 'develop'
			}
		    steps{
		        echo "del container"
		        bat script: '''For /F %%i IN ('docker ps ^| finstr 8080') do set contId = %%i
		        IF "%contId" == "" (
		            echo "no container running
		        )
		        ELSE(
		            docker stop %contId%
		            docker rm -f %contId%
		        )'''
		    }
		}
		stage("delete container feature"){
			when{
				brach 'feature'
			}
		    steps{
		        echo "del container"
		        bat script: '''For /F %%i IN ('docker ps ^| finstr 9090') do set contId = %%i
		        IF "%contId" == "" (
		            echo "no container running
		        )
		        ELSE(
		            docker stop %contId%
		            docker rm -f %contId%
		        )'''
		    }
		}
		stage('docker run develop'){
			when{
				brach 'develop'
			}
		    steps{
		        bat script: 'docker run -p 8080:80 dtr.nagarro.com:443/nagp-dotnet-vandnagarg:%BUILD_NUMBER%'
		    }
		}
		stage('docker run feature'){
			when{
				brach 'feature'
			}
		    steps{
		        bat script: 'docker run -p 9090:80 dtr.nagarro.com:443/nagp-dotnet-vandnagarg:%BUILD_NUMBER%'
		    }
		}
		stage('docker deployment develop'){
			steps{
				when{
					brach 'develop'
				}
				
				withEnv(['KubeConfig="./config"']) {
					bat script:'helm install vanchart --generate-name --set nodePOrt = 8080 --set image.repository=dotnet-vandna-nagp'
				}
				
			}
		}
		stage('docker deployment feature'){
			steps{
				when{
					brach 'feature'
				}
				
				withEnv(['KubeConfig="./config"']) {
					bat script:'helm install vanchart --generate-name --set nodePOrt = 9090 --set image.repository=dotnet-vandna-nagp'
				}
				
			}
		}
		
	}
}