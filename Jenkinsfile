pipeline{
       agent any

       tools{
              maven 'maven'
             // scannerHome 'sonarqube'

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
                            echo " Integration test success"
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

              withSonarQubeEnv('sonarqube'){

                  //   sh 'mvn sonar:sonar -Dsonar.host.url=http://localhost:9000'
                 //    sh 'mvn sonar:sonar'
                    // sh " mvn sonar:sonar -Dsonar.host.url=$SONARQUBE_URL:$SONARQUBE_PORT"
                    echo "sonar"
                     /*sh  " mvn sonar:sonar \
                       sonar.login=admin \
                       -Dsonar.password=admin \
                    -Dsonar.projectkey=jenkinssonar \
                     -Dsonar.host.url=http://localhost:9000/projects "*/

              }

              }

              }
              stage('Create and push container') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'dockerHub', usernameVariable: 'farahhachicha', passwordVariable: 'farahhachicha')]) {
    
            sh "mvn jib:build"
          
        }
      } 
    }

    stage('Anchore analyse') {
      steps {
        writeFile file: 'anchore_images', text: 'docker.io/maartensmeets/spring-boot-demo'
        anchore name: 'anchore_images'
      }
    }

    stage('Deploy to K8s') {
      steps {
        withKubeConfig([credentialsId: 'kubernetes-config']) {
          sh 'curl -LO "https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl"'
          sh 'chmod u+x ./kubectl'
          sh './kubectl apply -f k8s.yaml'
        }
      } 
    }

       }

}
