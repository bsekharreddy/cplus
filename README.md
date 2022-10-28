pipeline {
    agent any

    stages {
        stage('GIT SCM') {
            steps {
                git branch: 'main', url: 'https://github.com/AswathyRUllas99/googletest.git'
            }
        }
        stage('Cmake Build') {
            steps {
                sh 'cd /var/lib/jenkins/workspace/pipeline'
                sh 'pwd'
                sh 'sudo -s cmake CMakeLists.txt'
                sh 'sudo -s make'
                echo "CMake Build Success"
            }
        }
        stage('SonarQube analysis') {
          //tools {
            //sonarQube 'SonarQube4.7.0.2747'
          //}
          steps {
            //withSonarQubeEnv('SonarQube') {
              //sh "${scannerHome}/bin/sonar-scanner"
    		  //sh "sonar-scanner"
    		  echo "Analysis Completed"
            //}
          }
        }
    }
    post {
        failure {
        script {
            print("post jenkins stage")
            def mail_recipient_int= "aswathy.ullas@ltts.com"

            env.mail_recipient=mail_recipient_int + env.GIT_COMMITTER_EMAIL

            print "Commiter Mail_ID"

            print ("${env.GIT_COMMITTER_EMAIL}")

            print ("${env.mail_recipient}")

                if (env.BRANCH_NAME != 'master') {

                    emailext (

                            from: 'aswathyrullas99@gmail.com',

                            mimeType: 'text/html',

                            subject: '$PROJECT_NAME - Build # $BUILD_NUMBER',

                            body: """Hi Committer and integration team,
                                        Please check the failed build at:
                                        ${env.BUILD_URL}
                                        """,

                            to: "${env.mail_recipient}"

                            )

                        }
            
        }
        }
    }
}
