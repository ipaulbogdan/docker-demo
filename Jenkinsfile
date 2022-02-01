def branch_type = get_branch_type(env.BRANCH_NAME)
def revision = revision()

pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                script {
                    sh './mvnw clean install'
                }
            }
        }
        stage('Test') {
            steps {
                echo 'Testing..'
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying....'
            }
        }
    }
}

// Utility functions
def get_branch_type(String branch_name) {
    def dev_pattern = ".*develop"
    def release_pattern = ".*release/.*"
    def feature_pattern = ".*feature/.*"
    def hotfix_pattern = ".*hotfix/.*"
    def master_pattern = ".*master"
    if (branch_name =~ dev_pattern) {
        return "develop"
    } else if (branch_name =~ release_pattern) {
        return "release"
    } else if (branch_name =~ master_pattern) {
        return "master"
    } else if (branch_name =~ feature_pattern) {
        return "feature"
    } else if (branch_name =~ hotfix_pattern) {
        return "hotfix"
    } else {
        return null
    }
}

def mvn(String goals, additionalParameters = "" ) {
    def mvnHome = tool "M3"
    def javaHome = tool "Java17"
    def revision = revision()

    withEnv(["JAVA_HOME=${javaHome}", "PATH+MAVEN=${mvnHome}/bin"]) {
        sh "./mvnw -B " + additionalParameters + " -Drevision=${revision} ${goals}"
    }
}

def mvnRelease(String goals, additionalParameters = "" ) {
    def mvnHome = tool "M3"
    def javaHome = tool "Java17"
    def revision = revision()

    withEnv(["JAVA_HOME=${javaHome}", "PATH+MAVEN=${mvnHome}/bin"]) {
        configFileProvider( [configFile(fileId: 'chub-maven-settings.xml', variable: 'MAVEN_SETTINGS')]) {
            sh "./mvnw -s ${MAVEN_SETTINGS} -B " + additionalParameters + " -Drevision=${revision} ${goals}"
        }
    }
}

def revision() {
    def branchName = "${env.BRANCH_NAME}"
    def revision = branchName.substring(branchName.lastIndexOf('/') + 1)
    return "${revision}.${env.BUILD_NUMBER}"
}