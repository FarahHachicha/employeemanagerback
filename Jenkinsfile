pipeline{
       agent any
       tools{
              maven 'maven'
       }
       stages{
              stage('Build'){
                     steps{
                            sh 'mvn compile'
                            echo " Build success"
                     }
              }
              stage('Unit Test'){
                     steps{
                            sh 'mvn test'
                            echo " UNit test success"
                     }
                     post{
                           always{
                                  junit 'target/surefire-reports/TEST-*.xml'
                            }
                     }


              }
              stage ('Integration Test'){
                     steps {
                            sh 'mvn verify -Dsurefire.skip=true'
                            echo " Integrayion test success"
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



              withSonarQubeEnv('My SonarQube', envOnly: true){
                  //   sh 'mvn sonar:sonar -Dsonar.host.url=http://localhost:9000'
                    // sh 'mvn sonar:sonar'
                   //  echo "sonar"
                     echo ${env.SONAR_HOST_URL} 

              }

              }

              }
       }

}
