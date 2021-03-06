node {
    // Install the desired Go version
    def root = tool name: 'Go 1.12.4', type: 'go'

    try {
        ws("${JENKINS_HOME}/jobs/${JOB_NAME}/builds/${BUILD_ID}/") {
            withEnv(["GOPATH=${JENKINS_HOME}/jobs/${JOB_NAME}/builds/${BUILD_ID}", "GOROOT=${root}", "PATH+GO=${root}/bin"]) {
            
                stage('Checkout') {
                    echo 'Checking out SCM'
                    echo "$JENKINS_HOME"            
                    checkout scm
                }
                
                stage('Pre Test') {
                    echo 'Pulling Dependencies'

                    sh 'go version'
                    sh 'go get -u github.com/golang/dep/cmd/dep'
                    // sh 'go get -u golang.org/x/lint/golint'
                    // sh 'go get github.com/tebeka/go2xunit'
                    
                    // or -update
                    sh 'cd ${GOPATH}/src/server && pwd'
                }
        
                stage('Test') {
                    
                    // List all our project files with 'go list ./... | grep -v /vendor/ | grep -v github.com | grep -v golang.org'
                    // Push our project files relative to ./src
                    // sh 'cd $GOPATH && go list ./... | grep -v /vendor/ | grep -v github.com | grep -v golang.org > projectPaths'
                    
                    // Print them with 'awk '$0="./src/"$0' projectPaths' in order to get full relative path to $GOPATH
                    // def paths = sh returnStdout: true, script: """awk '\$0="./src/"\$0' projectPaths"""
                    
                    // echo 'Vetting'

                    // sh """cd $GOPATH && go tool vet ${paths}"""

                    // echo 'Linting'
                    // sh """cd $GOPATH && golint ${paths}"""
                    
                    echo 'Testing'
                    // sh """cd $GOPATH/src/server && go test -race -cover ${paths}"""
                    sh """cd $GOPATH/src/server && go test"""
                }
            
                stage('Build') {
                    echo 'Building Executable'
                
                    // Produced binary is $GOPATH/src/cmd/project/project
                    sh """cd $GOPATH/src/server && build.sh"""
                }
                
                stage('AWS Publish') {
                    // Do something with aws sdk
                }
            }
        }
    } catch (e) {
        // If there was an exception thrown, the build failed
        currentBuild.result = "FAILED"
        
    } finally {
        // Success or failure, always send notifications
        // TODO: notify the build result
        
        def bs = currentBuild.result ?: 'SUCCESSFUL'
        if (bs == 'SUCCESSFUL') {
            // TODO: notify the build result
        }
    }
}
