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
                -Dsonar.login=squ_344a8a235c135a9fa48250c7a04043b3e716225d"
                    
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
                            credentialsId: "7117635c-aa78-357f-b3a5-1eb6444958f6",
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
        
    }
    post {
        always {
            slackSend channel: 'fundamentos-de-devops', color: 'red', iconEmoji: 'ok_hand', message: 'Estimado Luis al fin lo reesolviste ...OK', teamDomain: 'sustantivagrupo', tokenCredentialId: 'slack_token', username: 'super_admin'
        }
    } 
}
