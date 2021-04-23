pipeline {
  agent any
  parameters {
    string(NEXUS_VERSION: 'nexus3')
    string(NEXUS_PROTOCOL: 'http')
    string(NEXUS_URL: '172.19.0.3:8081')
    string(NEXUS_REPOSITORY: 'maven-nexus-repo')
    string(NEXUS_CREDENTIAL_ID: 'e6072e08-87bc-481e-9e4a-55d506546356')
    }
  stages {
    stage('pull') {
      steps {
        git branch: "main",
        credentialsId: 'azima-git-ssh',
        url: 'git://github.com/VSAzima/spring-petclinic'
      }
    }
    stage('build') {
      steps {
        script {
          docker.image('maven:3.8.1-jdk-8').inside {
          sh 'mvn -B clean package'
        }
      }
    }
 }
      stage('push') {
        steps {
          script {
            pom = readMavenPom file: "pom.xml";
            filesByGlob = findFiles(glob: "target/*.${pom.packaging}");
            echo "${filesByGlob[0].name} ${filesByGlob[0].path} ${filesByGlob[0].directory} ${filesByGlob[0].length} ${filesByGlob[0].lastModified}"
            artifactPath = filesByGlob[0].path;
            artifactExists = fileExists artifactPath;
            //
            // if(artifactExists) {
            // echo "*** File: ${artifactPath}, group: ${pom.groupId}, packaging: ${pom.packaging}, version ${pom.version}";

            def nexusArtifactUploader(${params.NEXUS_VERSION}, ${params.NEXUS_PROTOCOL}, ${params.NEXUS_URL}, pom.groupId, pom.version, ${params.NEXUS_REPOSITORY}, ${params.NEXUS_CREDENTIAL_ID},
                      artifacts: [
                        [artifactId: pom.artifactId,
                        classifier: '',
                        file: artifactPath,
                        type: pom.packaging],
                        [artifactId: pom.artifactId,
                        classifier: '',
                        file: "pom.xml",
                        type: "pom"]
                             ]
                         );
                    //  } else {
                    //     error "*** File: ${artifactPath}, could not be found";
                    // }
                }
            }
        }
     }
  }
