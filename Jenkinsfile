pipeline {
    agent { label 'JDK_17_UBUNTU' }
    triggers { pollSCM ( '* * * * *')}
    parameters {
        choice (name: 'MAVEN_GOAL', choices: ['package', 'install', 'clean'], description: 'Maven Goal')
    }
    stages {
        stage('vcs') {
            steps {
                git url: 'https://github.com/comingapril/spring-petclinic.git',
                    branch: 'dev'
            }
        }
        stage('package') {
            tools {
                jdk 'JDK_17_UBUNTU'
            }
            steps {
                sh "mvn clean"
                sh "mvn ${params.MAVEN_GOAL}"
                }
        }
         stage('Sonar analysis') {
            steps {
                // performing sonarqube analysis with "withSonarQubeENV(<Name of Server configured in Jenkins>)"
                   withSonarQubeEnv('SONAR_CLOUD') {
                // requires SonarQube Scanner for Maven 3.2+
                // sh 'mvn org.sonarsource.scanner.maven:sonar-maven-plugin:3.2:sonar'
                   sh 'mvn clean package sonar:sonar -Dsonar.organization=springpetclinic11 -Dsonar.projectKey=springpetclinic11'
            }
            }
            }
        stage('post build') {
            steps {
                archiveArtifacts artifacts: '**/target/spring-petclinic-3.0.0-SNAPSHOT.jar',
                                 onlyIfSuccessful: true
                junit testResults: '**/surefire-reports/TEST-*.xml'
                stash name: 'spc-jar',
                      includes: '**/target/spring-petclinic-3.0.0-SNAPSHOT.jar'
            }			
        }	
        stage('ansibe') {
            agent { label 'JDK_8' }
            steps {
                unstash name: 'spc-jar'
            }
        }
    }
}
