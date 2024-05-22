 //GLOVAL_VARIABLES_Start
def imageName="192.168.44.44:8082/docker_registry/frontend"
def dockerTag=""
def dockerRegistry="https://192.168.44.44:8082"
def registryCredentials="artifactory"
//GLOVAL_VARIABLES_End
pipeline { //PIPELINE_START
    agent 
    {
        label 'agent'
    }
 //START-ENV
    environment 
    {
        PIP_BREAK_SYSTEM_PACKAGES = 1
        scannerHome = tool 'SonarQube'
    }
 //START-END
    stages { //STAGESSSSSSSSSSSSSSSSSSSSSSSSS-START
        stage('Get Code') 
        {
            steps 
            {
                git branch: 'jenkinsfile', url: 'https://github.com/Panda-Academy-Core-2-0/Frontend'
            }
        }
  //STAGE-END
  //STAGE-START
        stage('Unit tests') 
        {
            steps {
                sh "pip3 install -r requirements.txt"
                sh "python3 -m pytest --cov=. --cov-report xml:test-results/coverage.xml --junitxml=test-results/pytest-report.xml"
                  }
        }
  //STAGE-END
  //STAGE-START
        stage('Sonarqube analysis') {
            steps {
                withSonarQubeEnv('SonarQube') 
                {
                    sh "${scannerHome}/bin/sonar-scanner"
                }
                  }
        }
  //STAGE-END
  //STAGE-START
        stage('Build application image') {
            steps 
            {
                script 
                {
                  dockerTag = "RC-${env.BUILD_ID}"
                  applicationImage = docker.build("$imageName:$dockerTag")
                }
            }
        }
  //STAGE-END
  //STAGE-START
        stage ('Pushing image to docker registry') {
            steps 
            {
                script 
                {
                    docker.withRegistry("$dockerRegistry", "$registryCredentials") 
                    {
                        applicationImage.push()
                        applicationImage.push('latest')
                    }
                }
            }
        } 
  //STAGE-END
    } //STAGESSSSSSSSSSSSSSSSSSSS_END
 //POST-START
    post {
        always 
        {
            junit testResults: "test-results/*.xml"
            cleanWs()
         }
        }
 //POST-END
}