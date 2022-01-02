def imageName = 'example'

pipeline{
    agent{
        label 'java8'
    }

    stages{
        stage('Checkout'){
            steps{
                checkout scm
            }
        }

        stage('Build'){
            steps {
                script {
                    echo 'Pulling...' + env.BRANCH_NAME
                    def mvnHome = tool 'Maven 3.5.2'
                        def targetVersion = getDevVersion()
                        print 'target build version...'
                        print targetVersion
                        sh "'${mvnHome}/bin/mvn' -Dintegration-tests.skip=true -Dbuild.number=${targetVersion} clean package"
                        def pom = readMavenPom file: 'pom.xml'
                        // get the current development version
                        developmentArtifactVersion = "${pom.version}-${targetVersion}"
                        print pom.version
                        // execute the unit testing and collect the reports
                        //junit '**//*target/surefire-reports/TEST-*.xml'
                        archive 'target*//*.jar'
                }
            }
                    
        }
        
        
        ///start deploy///
        
        stage('Deploy Dev'){
            steps {
                script {
                    echo 'deploying to dev'
                    sh "mkdir -p /tmp/dev"
                    sh "cp -r target/*.jar /tmp/dev/"
                    
                }
            }
                    
        }        
        
        stage('Deploy QA'){
            steps {
                script {
                    echo 'deploying to QA'
                    sh "mkdir -p /tmp/qa"
                    sh "cp -r target/*.jar /tmp/qa/"
                    
                }
            }
                    
        }

        ////end deploy ///
        
        

    }
}

def getDevVersion() {
    def gitCommit = sh(returnStdout: true, script: 'git rev-parse HEAD').trim()
    def versionNumber;
    if (gitCommit == null) {
        versionNumber = env.BUILD_NUMBER;
    } else {
        versionNumber = gitCommit.take(8);
    }
    print 'build  versions...'
    print versionNumber
    return versionNumber
}
