node {
    try {
        ws("${JENKINS_HOME}/jobs/${JOB_NAME}/builds/${BUILD_ID}/") {
            withEnv(["GOPATH=${JENKINS_HOME}/jobs/${JOB_NAME}/builds/${BUILD_ID}"]) {
            
                stage('Checkout') {
                    echo 'Checking out SCM'
                    echo "$JENKINS_HOME"
                    checkout scm
                }
                
                stage('Pre Test') {
                    echo 'Pulling Dependencies'

                    sh 'sudo su'
                    sh 'yum update'
                    sh 'wget https://dl.google.com/go/go1.12.2.linux-amd64.tar.gz'
                    sh 'tar -xzf go1.12.2.linux-amd64.tar.gz'
                    sh 'rm go1.12.2.linux-amd64.tar.gz'

                    sh 'source /etc/profile && source ~/.bash_profile && env'
                    sh 'go version'
                    sh 'go get -u github.com/golang/dep/cmd/dep'
                    sh 'go get -u github.com/golang/lint/golint'
                    sh 'go get github.com/tebeka/go2xunit'
                    
                    // or -update
                    sh 'cd ${GOPATH}/src/cmd/project/ && dep ensure' 
                }
        
                stage('Test') {
                    
                    // List all our project files with 'go list ./... | grep -v /vendor/ | grep -v github.com | grep -v golang.org'
                    // Push our project files relative to ./src
                    sh 'cd $GOPATH && go list ./... | grep -v /vendor/ | grep -v github.com | grep -v golang.org > projectPaths'
                    
                    // Print them with 'awk '$0="./src/"$0' projectPaths' in order to get full relative path to $GOPATH
                    def paths = sh returnStdout: true, script: """awk '\$0="./src/"\$0' projectPaths"""
                    
                    echo 'Vetting'

                    sh """cd $GOPATH && go tool vet ${paths}"""

                    echo 'Linting'
                    sh """cd $GOPATH && golint ${paths}"""
                    
                    echo 'Testing'
                    sh """cd $GOPATH && go test -race -cover ${paths}"""
                }
            
                stage('Build') {
                    echo 'Building Executable'
                
                    // Produced binary is $GOPATH/src/cmd/project/project
                    sh """cd $GOPATH/src/cmd/project/ && go build -ldflags '-s'"""
                }
                
                stage('AWS Publish') {
                    // Do
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
