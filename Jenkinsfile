pipeline{
    //Directives
    agent any
    tools {
        maven 'maven'
    }
  environment{
       ArtifactId = readMavenPom().getArtifactId()
       Version = readMavenPom().getVersion()
       Name = readMavenPom().getName()
       GroupId = readMavenPom().getGroupId()
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
                    def NexusRepo = Version.endsWith("RELEASE") ? "UttejDevOps-RELEASE" : "UttejDevOps-SNAPSHOT"
                nexusArtifactUploader artifacts:
                    [[artifactId: "${ArtifactId}",
                      classifier: '',
                      file: "target/${ArtifactId}-${Version}.war",
                      type: 'war']],
                    credentialsId: '5d59cfd2-1ac5-4fcd-a730-650b79548a1b',
                    groupId: "${GroupId}",
                    nexusUrl: '172.20.10.153:8081',
                    nexusVersion: 'nexus3',
                    protocol: 'http',
                    repository: "${NexusRepo}",
                    version: "${Version}"
                }
                
                
                    
              /*  script{
                    def NexusRepo = Version.endsWith("SNAPSHOT") ? "UttejDevOps-SNAPSHOT" : "UttejDevOps-RELEASE"
            nexusArtifactUploader artifacts:
                [[artifactId: "${ArtifactId}", 
                classifier: '', 
                file: "target/${ArtifactId}-${Version}.war", 
                type: 'war']],
                credentialsId: '8ad40b34-130a-4f2f-a590-4405c14ff7f7',
                groupId: "${GroupId}",
                nexusUrl: '172.20.10.153:8081',
                nexusVersion: 'nexus3',
                protocol: 'http',
                repository: "${NexusRepo}", 
                version: "${Version}"
                    echo "${NexusRepo}"
                    echo "target/${ArtifactId}-${Version}.war"
                } */
            }
        }
        
        stage("deploying the artifact"){
                    steps{
                    sshPublisher(publishers:
                                 [sshPublisherDesc(configName: 'ansible-control-node',
                                                   transfers: [sshTransfer(cleanRemote: false,
                                                                           excludes: '',
                                                                           execCommand: 'ansible-playbook /opt/playbooks/downloadanddeploy.yaml -i /opt/playbooks/hosts',
                                                                           execTimeout: 120000,
                                                                           flatten: false,
                                                                           makeEmptyDirs: false,
                                                                           noDefaultExcludes: false,
                                                                           patternSeparator: '[, ]+',
                                                                           remoteDirectory: '',
                                                                           remoteDirectorySDF: false,
                                                                           removePrefix: '',
                                                                           sourceFiles: '')],
                                                   usePromotionTimestamp: false,
                                                   useWorkspaceInPromotion: false,
                                                   verbose: false)])
                    }
                }
        //deploying appication using docker
        stage("deploying the artifact using docker"){
                    steps{
                    sshPublisher(publishers:
                                 [sshPublisherDesc(configName: 'ansible-control-node',
                                                   transfers: [sshTransfer(cleanRemote: false,
                                                                           excludes: '',
                                                                           execCommand: 'ansible-playbook /opt/playbooks/deployusingdocker.yml -i /opt/playbooks/hosts',
                                                                           execTimeout: 120000,
                                                                           flatten: false,
                                                                           makeEmptyDirs: false,
                                                                           noDefaultExcludes: false,
                                                                           patternSeparator: '[, ]+',
                                                                           remoteDirectory: '',
                                                                           remoteDirectorySDF: false,
                                                                           removePrefix: '',
                                                                           sourceFiles: '')],
                                                   usePromotionTimestamp: false,
                                                   useWorkspaceInPromotion: false,
                                                   verbose: false)])
                    }
                }
        //stage4: printing environment variables
        /*stage("Print environment variable"){
            steps{
                echo "Artifact ID is '${ArtifactId}'"
                echo "version is '${Version}'"
                echo "Group ID is '${GroupId}'"
                echo "Name is '${Name}'"
            }
        }*/

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
