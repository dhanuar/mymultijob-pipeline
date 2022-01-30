pipeline{
    agent any
    triggers {
      pollSCM '* * * * *'
    }
    stages{
        stage("maven build"){
            when{
                expression{
                    env.BRANCH_NAME.equals("develop") ||
                    env.BRANCH_NAME.startsWith("feature")
                }
            }
            steps{
              sh "mvn package"
            }
        }
        stage("sonarQube Analysis"){
            when{
                expression{
                    env.BRANCH_NAME.equals("develop") ||
                    env.BRANCH_NAME.startsWith("feature")
                }
            }
            steps{
              withSonarQubeEnv('sonar7') {
              sh "mvn sonar:sonar"
             }
            }
        }
        stage("sonarQube Status"){
            when{
                branch "develop2"
            }
            steps{
              timeout(time: 1, unit: 'HOURS') {
                //    For this to work, we should add webhook in sonar
                //    http://172.31.3.50:8080/sonarqube-webhook/
                script{
                    def qg = waitForQualityGate()
                    if (qg.status != 'OK') {
                        error "Pipeline aborted due to quality gate failure: ${qg.status}"
                      }
                    }
                }
            }
        }
        stage("nexus"){
            when{
                branch "develop"
            }
            steps{
              echo "nexus upload artifacts..."
            }
        }
        stage("deploy to qa"){
            when{
                branch "qa"
            }
            steps{
              echo "deplyment to qa"
            }
        }
        stage("deploy to production"){
            when{
                branch "master"
            }
            steps{
              echo "deplyment to master"
            }
        }
    }
}
