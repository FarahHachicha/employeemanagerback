
pipeline{
       agent any
       tools{
              maven 'maven'
       }
       stages{
              stage('Build'){
                     steps{
                            sh 'mvn compile'
                     }
              }
              stage('Unit Test'){
                     steps{
                            sh 'mvn test'
                     }
                     post{
                           always{
                                  junit 'target/surefire-reports/TEST-tech.getarrays.employeemanager.EmployeemanagerApplicationTests.xml'
                            }
                     }


              }
              stage ('Integration Test'){
                     steps {
                            sh 'mvn verify -Dsurefire.skip=true'
                     }
                     post{
                           always{
                                  junit (testResults:'target/failsafe-reports/*.xml',allowEmptyResults: true)
                            }
                            success{
                                   stash(name : 'artifact' , includes :'target/*.jar')
                                   stash(name : 'pom' , includes :'pom.xml')
                                   archiveArtifacts 'target/*.jar'

                            }
                     }
              }
              stage ('SonarQube'){
              steps{
              withSonarQubeEnv('My SonarQube Server'){
              sh 'mvn sonar:sonar '
              }

              }

              }
       }

}