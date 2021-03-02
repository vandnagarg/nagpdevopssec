pipeline{
	agent any
	
	environment{
	    scannerhome = tool name: 'sonar_scanner_dotnet' , type:'hudson.plugins.sonar.MsBuildSQRunnerInstallation'
	}
	
	stages{
		stage('checkout'){
			steps{
				git 'https://github.com/vandnagarg/nagpdevopssec'
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
                    bat script : 'dotnet "%scannerhome%/sonarscanner.msbuild.dll'
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
                    bat script : 'dotnet "%scannerhome%/sonarscanner.msbuild.dll'
                }
		    }
		}
		stage("docker build"){
		    steps{
		        echo "docker build"
		        bat script: 'docker build -t dtr.nagarro.com:443/nagp-dotnet-vandnagarg:%BUILD_NUMBER% ./Nagpdotnet'
		    }
		}
		stage("delete container"){
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
		stage('docker run'){
		    steps{
		        bat script: 'docker run -p 9090:80 dtr.nagarro.com:443/nagp-dotnet-vandnagarg:%BUILD_NUMBER%'
		    }
		}
		stage("docker build"){
		    steps{
		        echo "docker build"
		        bat script: 'docker build ./tree/develop/Nagpdotnet'
		    }
		}
		stage("delete container"){
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
		stage('docker run'){
		    steps{
		        bat script: 'docker run -p 9090:80 dtr.nagarro.com:443/nagp-dotnet-vandnagarg:%BUILD_NUMBER%'
		    }
		}
	}
}