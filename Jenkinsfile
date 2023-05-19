pipeline
    {
        agent any
        
         options {
        office365ConnectorWebhooks([
            [name: "Office 365", url: "https://bluebinaries.webhook.office.com/webhookb2/12240007-70fd-4219-b327-504e0903236f@2771cf1c-6046-4140-b5eb-779adb5c72fa/JenkinsCI/398cfea620094d7a8f795df71386cb09/1d0221d0-6d31-4dd7-976a-de31d6ee6aee", notifyBackToNormal: true, notifyFailure: true, notifyRepeatedFailure: true, notifySuccess: true, notifyAborted: true]
        ])
    }
stages{
        stage('clean workspace')
            {
                steps  
                {
                    cleanWs()
                    sh 'cd /var/lib/jenkins/workspace/*'
                }
            }
        stage('Cloning')
            {
                steps
                {  
                    echo "Clone started"
                    // Clone the repository using Git
                    sh 'cd /var/lib/jenkins/workspace/ && git clone https://github.com/shredhanbhar/FormulaEvaluator.git'
                   // sh 'rm -rf /var/lib/jenkins/workspace/unit-test/lib'
                   // sh 'mkdir /var/lib/jenkins/workspace/unit-test/lib'
                   // sh 'cd /var/lib/jenkins/workspace/unit-test/lib/ && git clone https://github.com/google/googletest.git'
                    echo "Clone done"
                }
            }

        stage('Build code')

            {
                steps
                {   
                    // Run the build command for your C/C++ project
                    echo "Build started"
                    // sh 'mkdir -r /var/lib/jenkins/workspace/unit-test/build'
                    sh 'cd /var/lib/jenkins/workspace/FormulaEvaluator/build/ && cmake .. && make'
                    echo "Build ended"
                }
                     post{
                             always{
                             mail to: "shreya.dhanbhar@bluebinaries.com",
                             subject: "Build Success",
                             body: "${BUILD_NUMBER}_Passed!"
                             }
                         }

            }
                 

        stage('Junit-test')
            {
                steps
                {
                    // Run unit tests for your project
                    sh 'cd /var/lib/jenkins/workspace/FormulaEvaluator/build/ && make test'
                   sh 'cd /var/lib/jenkins/workspace/FormulaEvaluator/build/tst/ && ./ExampleProject_tst --gtest_output=xml'
                }
              //   post {
                //        always {
                  //              junit(testResults: '/var/lib/jenkins/workspace/FormulaEvaluator/build/tst/*.xml', allowEmptyResults : true)
    //}
  //}
            }

        stage('Build tar')
            {
                steps
                {
                    //building tar directory 
                    sh 'cd /var/lib/jenkins/workspace/FormulaEvaluator/ && tar -czvf /var/lib/jenkins/workspace/FormulaEvaluator/build.tar.gz /var/lib/jenkins/workspace/FormulaEvaluator/build/'
                    echo "tar directory generated"
                }
            }    

        stage('Upload Artifacts to Nexus repo')
            {
                steps
                {
                    nexusArtifactUploader artifacts: [[artifactId: '${BUILD_NUMBER}', classifier: 'build.tar.gz', file: '/var/lib/jenkins/workspace/FormulaEvaluator/build.tar.gz', type: 'tar']], credentialsId: 'nexus', groupId: 'cmake-repo', nexusUrl: 'localhost:8081', nexusVersion: 'nexus3', protocol: 'http', repository: 'unit-test', version: '1'
                }
                post{
                         always{
                         mail to: "shreya.dhanbhar@bluebinaries.com",
                         subject: "Artifacts Uploaded",
                         body: "${BUILD_NUMBER}_Passed! Uploaded Artifacts to Nexus repo successfully"
                         }
                   }
            
            }

        stage('Generating Doxygen Documentation')
            {
                steps
                {
                    sh 'cd /var/lib/jenkins/workspace/FormulaEvaluator/build/ && make docs'
                    sh 'chmod -R 777 /var/lib/jenkins/workspace/FormulaEvaluator/*'        
                    echo "index.html created"  
            
                }
              post{  
                        always{
                        mail to: "shreya.dhanbhar@bluebinaries.com",
                        subject: "Doxygen Documentation ",
                        body: "${BUILD_NUMBER}_PASS! Generated Doxygen Documentation"
                            }
              }
            
            }
             stage('Doxygen tar')
            {
                steps
                {
                    //building tar directory 
                    sh 'cd /var/lib/jenkins/workspace/FormulaEvaluator/docs/html && tar -czvf /var/lib/jenkins/workspace/FormulaEvaluator/docs/html.tar.gz /var/lib/jenkins/workspace/FormulaEvaluator/docs/html/'
                    echo "tar directory generated for doxygen"
                }
            } 
            stage('Upload Doxygen tar to Nexus repo')
            {
                steps
                {
                    nexusArtifactUploader artifacts: [[artifactId: '${BUILD_NUMBER}', classifier: 'html.tar.gz', file: '/var/lib/jenkins/workspace/FormulaEvaluator/docs/html.tar.gz', type: 'tar']], credentialsId: 'nexus', groupId: 'cmake-repo', nexusUrl: 'localhost:8081', nexusVersion: 'nexus3', protocol: 'http', repository: 'unit-test', version: '1'
                }
                post{
                         always{
                         mail to: "shreya.dhanbhar@bluebinaries.com",
                         subject: "Documents Uploaded",
                         body: "${BUILD_NUMBER}_Passed! Uploaded Generated Doxygen Documents to Nexus repo successfully"
                         }
                   }
            
            }   


      }
                post{
                        failure{
                        mail to: "shreya.dhanbhar@bluebinaries.com",
                        subject: "Pipeline Failed",
                        body: "${BUILD_NUMBER}_FAIL!, Something is wrong with Pipeline"
                            }
                    }
           
    }
