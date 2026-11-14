def registry = 'https://trialn3tztq.jfrog.io/'

pipeline {
    agent any

    environment {
        PATH = "/opt/maven/bin:$PATH"
    }

    stages {

        stage('build') {
            steps {
                echo "---------- build started ----------"

                sh "mvn clean deploy -DskipTests"

                echo "---------- build completed ----------"
            }
        }

        stage('test') {
            steps {
                echo "---------- unit test started ----------"

                sh "mvn surefire-report:report"

                echo "---------- unit test completed ----------"
            }
        }

        stage('SonarQube analysis') {
            environment {
                scannerHome = tool 'sonarqube-scanner'
            }

            steps {
                withSonarQubeEnv('saidemy-sonarqube-server') {
                    sh "${scannerHome}/bin/sonar-scanner"
                }
            }
        }

        stage('Jar Publish') {
            steps {
                script {
                    echo "---------- Jar Publish Started ----------"

                    def server = Artifactory.server(
                        url: registry + "/artifactory",
                        credentialsId: "artifact-cred"
                    )

                    def properties = "buildid=${env.BUILD_ID},commitid=${GIT_COMMIT}"

                    def uploadSpec = """{
                        "files": [
                            {
                                "pattern": "jarstagging/(*)",
                                "target": "sai-libs-release-local/{1}/",
                                "flat": "false",
                                "props": "${properties}",
                                "exclusions": ["*.sha1", "*.md5"]
                            }
                        ]
                    }"""

                    def buildInfo = server.upload(uploadSpec)

                    buildInfo.env.collect()

                    server.publishBuildInfo(buildInfo)

                    echo "---------- Jar Publish Ended ----------"
                }
            }
        }
    }
}
