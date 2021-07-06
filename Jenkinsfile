pipeline{
    //Directives
    agent any
    tools {
        maven 'maven'
    }
    environment{
    artifactId = readMavenPom().getArtifactId()
    version = readMavenPom().getVersion()
    name = readMavenPom().getName()
    groupId = readMavenPom().getGroupId()
    }

    stages {
        // Specify various stage with in stages

        // stage 1. Build
        stage ('Build'){
            steps {
                sh 'mvn clean install package'
            }
        }

        // Stage2 : Testing
        stage ('Test'){
            steps {
                echo ' testing......'

            }
        }
        //stage3: push artifact to nexus
        stage("pushing artifact to nexus"){
            steps {
                script{
                    def NexusRepo = Version.endsWith("SNAPSHOT")? "UttejDevOps-SNAPSHOT" : "UttejDevOpsLab-RELEASE"
            nexusArtifactUploader artifacts: [[artifactId: '${artifactId}',
                                               classifier: '',
                                               file: 'target/UttejDevOpsLab-0.0.1-SNAPSHOT.war',
                                               type: 'war']],
                credentialsId: '8ad40b34-130a-4f2f-a590-4405c14ff7f7',
                groupId: '${groupId}',
                nexusUrl: '172.20.10.124:8081',
                nexusVersion: 'nexus3',
                protocol: 'http',
                repository: '${NexusRepo}',
                version: '${version}'
                }
            }
        }
        //stage4: printing environment variables
        stage("Print environment variable"){
            steps{
                echo "Artifact ID is '${artifactId}'"
                echo "version is '${version}'"
                echo "Group ID is '${groupId}'"
                echo "Name is '${name}'"
            }
        }

        // Stage3 : Publish the source code to Sonarqube
       /* stage ('Sonarqube Analysis'){
            steps {
                echo ' Source code published to Sonarqube for SCA......'
                withSonarQubeEnv('sonarqube'){ // You can override the credential to be used
                     sh 'mvn sonar:sonar'
                }

            }
        }
*/
        
        
    }

}
