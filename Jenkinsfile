pipeline{
	agent any
	
	environment{
	    scannerhome = tool name: 'sonar_scanner_dotnet' , type:'hudson.plugins.sonar.MsBuildSQRunnerInstallation'
		
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
		
		stage("start sonar"){
		    steps{
		        withSonarQubeEnv('Test_Sonar') {
                    echo "start sonar"
                    bat script : 'dotnet "%scannerhome%/SonarScanner.MSBuild.dll" begin /k:dontetnagpvandnauni /n:dontetnagpvandnauni /v:1.0'
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
                    bat script : 'dotnet "%scannerhome%/SonarScanner.MSBuild.dll" end'
                }
		    }
		}
		stage("test"){
			steps{
				bat script:'dotnet test'
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
				branch 'develop'
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
				branch 'feature'
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
				branch 'develop'
			}
		    steps{
		        bat script: 'docker run -p 8080:80 dtr.nagarro.com:443/nagp-dotnet-vandnagarg:%BUILD_NUMBER%'
		    }
		}
		stage('docker run feature'){
			when{
				branch 'feature'
			}
		    steps{
		        bat script: 'docker run -p 9090:80 dtr.nagarro.com:443/nagp-dotnet-vandnagarg:%BUILD_NUMBER%'
		    }
		}
		stage('docker deployment develop'){
			steps{
				when{
					branch 'develop'
				}
				
				withEnv(['KubeConfig="./config"']) {
					bat script:'helm install vanchart --generate-name --set nodePOrt = 8080 --set image.repository=dotnet-vandna-nagp'
				}
				
			}
		}
		stage('docker deployment feature'){
			steps{
				when{
					branch 'feature'
				}
				
				withEnv(['KubeConfig="./config"']) {
					bat script:'helm install vanchart --generate-name --set nodePOrt = 9090 --set image.repository=dotnet-vandna-nagp'
				}
				
			}
		}
		
	}
}