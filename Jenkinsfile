pipeline {
    agent any
    stages {
        stage('Initialize'){
            steps{
                echo "Esta es el inicio"
            }
        }
        stage('Sonarqube quality check'){
            steps{
                script {
                sh "/var/jenkins_home/sonar/bin/sonar-scanner \
                -Dsonar.projectKey=Demo1 \
                -Dsonar.projectName=Demo1 \
                -Dsonar.projectVersion=1 \
                -Dsonar.scm.disabled=true \
                -Dsonar.sources=. \
                -Dsonar.language=java \
                -Dsonar.java.binaries=./target/classes \
                -Dsonar.sourceEncoding=UTF-8 \
                -Dsonar.host.url=http://192.168.100.13:9000 \
                -Dsonar.login=squ_1b7bd1baab237ff86d7e54c37f9aa0f24eb941b3"
                    
                }
            }
        }    
        stage('Build') {
            steps {
                sh 'mvn -B package'
            
            }
        }
            
        stage('Test') {
            steps {
                sh "mvn clean verify" 
            
            }
        } 
        stage("Publish to Nexus Repository Manager") {
            steps {
                script {
                    pom = readMavenPom file: "pom.xml";
                    filesByGlob = findFiles(glob: "target/*.${pom.packaging}");
                    echo "${filesByGlob[0].name} ${filesByGlob[0].path} ${filesByGlob[0].directory} ${filesByGlob[0].length} ${filesByGlob[0].lastModified}"
                    artifactPath = filesByGlob[0].path;
                    artifactExists = fileExists artifactPath;
                    if(artifactExists) {
                        echo "*** File: ${artifactPath}, group: ${pom.groupId}, packaging: ${pom.packaging}, version ${pom.version}";
                        nexusArtifactUploader(
                            nexusVersion: "nexus3",
                            protocol: "http",
                            nexusUrl: "192.168.100.13:8081",
                            groupId: pom.groupId,
                            version: pom.version,
                            repository: "repo-nexus",
                            credentialsId: "d085a0a2-dbd8-487b-a2c1-6115c90a1e6e",
                            artifacts: [
                                [artifactId: pom.artifactId,
                                        classifier: '',
                                        file: artifactPath,
                                        type: pom.packaging]
                            ]
                        );
                    } else {
                        error "*** File: ${artifactPath}, could not be found";
                    }
                }
            }
        
        } 
        post {
            always{
                slackSend color: "#439FE0", channel: "#fundamentos-de-devops", "Build deployed successfully - ${env.JOB_NAME} ${env.BUILD_NUMBER} (<${env.BUILD_URL}|Open>)"
            }
        } 

        }
}



