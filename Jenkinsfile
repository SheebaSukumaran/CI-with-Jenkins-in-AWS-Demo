pipeline {
    agent any
    environment {
    NEXUS_VERSION = "nexus3"
    NEXUS_PROTOCOL = "http"
    NEXUS_URL = "http://35.230.141.52:8081/"
    NEXUS_REPOSITORIES = "CICDRepo"
    NEXUS_CREDENTIAL_ID = "admin"
    CREDENTIALSID = "admin"
    }
    stages {
        stage('CodeQuality-SonarQube') {
            steps {
                withSonarQubeEnv('sonarqube'){
                echo "SonarQube.."
                sh 'mvn sonar:sonar'
                }
            }
        }
        stage('Build') {
            steps {
                echo 'Building..'
                sh 'mvn compile'
            }
        }
        stage('Clean and Package'){
            steps {
            echo "Cleaning and Packaging..."
            sh 'mvn clean package'
            }
        }
        stage('Test') {
            steps {
                echo 'Testing..'
                sh 'mvn test'
            }
        }
        stage("publish to nexus") {
         steps {
             script {
             pom = readMavenPom file: "pom.xml";
             filesByGlob = findFiles(glob: "project/target/*.war");
             echo "${filesByGlob[0].name} ${filesByGlob[0].path} ${filesByGlob[0].directory} ${filesByGlob[0].length} ${filesByGlob[0].lastModified}"
             artifactPath = filesByGlob[0].path;
             artifactExists = fileExists artifactPath;
             if(artifactExists) {
                 echo "*** File: ${artifactPath}, group: ${pom.groupId}, packaging: ${pom.packaging}, version: ${pom.version}"
                 nexusArtifactUploader(
                 nexusVersion: NEXUS_VERSION,
                 protocol: NEXUS_PROTOCOL,
                 nexusUrl: NEXUS_URL,
                 groupId: pom.groupId,
                 version: "1.2",
                 repository: NEXUS_REPOSITORIES,
                 credentialsId: NEXUS_CREDENTIAL_ID,
                 artifacts: [
                     [artifactId: pom.artifactId,
                     classifier: '',
                     file: artifactPath,
                     type: "war"],
                     [artifactId: pom.artifactId,
                     classifier: '',
                     file: "pom.xml",
                     type: "pom"]
                ]
				);
              } else {
                     error "*** File: ${artifactPath}, could not be found";
                }

             }
         }
      }
		
        stage('Deploy') {
            steps {
                echo 'Deploying....'
            }
        }
    }
}
