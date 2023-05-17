pipeline
    {
        agent any
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
                    sh 'cd /var/lib/jenkins/workspace/ && git clone https://github.com/shredhanbhar/unit-test.git'
                    sh 'rm -rf /var/lib/jenkins/workspace/unit-test/lib'
                    sh 'mkdir /var/lib/jenkins/workspace/unit-test/lib'
                    sh 'cd /var/lib/jenkins/workspace/unit-test/lib/ && git clone https://github.com/google/googletest.git'
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
                    sh 'cd /var/lib/jenkins/workspace/unit-test/build/ && cmake .. && make'
                    echo "Build ended"
                }
                     post{
                             always{
                             mail to: "shreya.dhanbhar@bluebinaries.com, nikita.mankar@bluebinaries.com",
                             subject: "Build Success",
                             body: "${BUILD_NUMBER}_Passed!"
                             }
                         }

            }
                 

        stage('Unit test')
            {
                steps
                {
                    // Run unit tests for your project
                    sh 'cd /var/lib/jenkins/workspace/unit-test/build/ && make test'
                }
                
            }

        stage('Build tar')
            {
                steps
                {
                    //building tar directory 
                    sh 'cd /var/lib/jenkins/workspace/unit-test/ && tar -czvf /var/lib/jenkins/workspace/unit-test/build.tar.gz /var/lib/jenkins/workspace/unit-test/build/'
                    echo "tar directory generated"
                }
            }    

        stage('Upload Artifacts to Nexus repo')
            {
                steps
                {
                    nexusArtifactUploader artifacts: [[artifactId: '${BUILD_NUMBER}', classifier: 'build.tar.gz', file: '/var/lib/jenkins/workspace/unit-test/build.tar.gz', type: 'tar']], credentialsId: 'nexus', groupId: 'cmake-repo', nexusUrl: 'localhost:8081', nexusVersion: 'nexus3', protocol: 'http', repository: 'unit-test', version: '1'
                }
                post{
                         always{
                         mail to: "shreya.dhanbhar@bluebinaries.com, nikita.mankar@bluebinaries.com",
                         subject: "Artifacts Uploaded",
                         body: "${BUILD_NUMBER}_Passed! Uploaded Artifacts to Nexus repo successfully"
                         }
                   }
            
            }

        stage('Doxygen')
            {
                steps
                {
                    sh 'cd /var/lib/jenkins/workspace/unit-test/build/ && make docs'
                    sh 'chmod -R 777 /var/lib/jenkins/workspace/unit-test/*'        
                    echo "index.html created"         
                }
              post{  
                        always{
                        mail to: "shreya.dhanbhar@bluebinaries.com, nikita.mankar@bluebinaries.com",
                        subject: "build completed with 0 failures",
                        body: "${BUILD_NUMBER}_PASS!"
                            }
                    }
            
            }
      }
                post{
                        failure{
                        mail to: "shreya.dhanbhar@bluebinaries.com, nikita.mankar@bluebinaries.com",
                        subject: "Failed Pipeline",
                        body: "${BUILD_NUMBER}_FAIL!, Something is wrong with Pipeline"
                            }
                    }
                        
    }
