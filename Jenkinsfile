pipeline {
    agent any
   
  //  environment{
    //    SCANNER_HOME= tool 'sonar-scanner'
    //}

    stages {
        stage('git-checkout') {
            steps {
                git branch: 'main', changelog: false, poll: false, url: 'https://github.com/devopssrinu464/to-do-app.git'
            }
        }

    //stage('Sonar Analysis') {
      //      steps {
        //           sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.url=URL_OF_SONARQUBE -Dsonar.login=TOKEN_OF_SONARQUBE -Dsonar.projectName=to-do-app \
          //         -Dsonar.sources=. \
            //       -Dsonar.projectKey=to-do-app '''
              // }
            //}
           
		stage('OWASP Dependency Check') {
            steps {
               dependencyCheck additionalArguments: '--scan ./', odcInstallation: 'DP'
                    dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }
     

         stage('Docker Build') {
            steps {
               script{
                   withDockerRegistry(credentialsId: '84d65fc5-2e73-4325-aabf-e84f7b781fbd') {
                    sh "docker build -t  todoapp:latest -f backend/Dockerfile . "
                    sh "docker tag todoapp:latest devopssep/todoapp:latest "
                 }
               }
            }
        }

        stage('Docker Push') {
            steps {
               script{
                   withDockerRegistry(credentialsId: '84d65fc5-2e73-4325-aabf-e84f7b781fbd') {
                    sh "docker push  devopssep/todoapp:latest "
                 }
               }
            }
        }
        stage('trivy') {
            steps {
               sh " trivy devopssep/todoapp:latest"
            }
        }
		stage('Deploy to Docker') {
            steps {
               script{
                   withDockerRegistry(credentialsId: '84d65fc5-2e73-4325-aabf-e84f7b781fbd') {
                    sh "docker run -d --name to-do-app -p 4000:4000 devopssep/todoapp:latest "
                 }
               }
            }
        }

    }
}
