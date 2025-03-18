pipeline {
    agent any
    
    tools {
        maven "M3"
        jdk "JDK17"
        
}
    
    stages {
        stage('Git Clone') {
            steps {
                echo 'Git Clone'
                git url: 'https://github.com/lBopul/spring-petclinic.git',
                branch: 'main'
            }
            post {
                success {
                    echo 'Git Clone Success'
                }
                failure {
                    echo 'Git Clone Fail'
                }
            }
        }
        
        stage('Maven Build') {
            steps {
                echo 'Maven Build'
                sh 'mvn -Dmaven.test.failure.ignore=true clean package' // test error 무시
            }
        }

        // Docker Image 생성
        stage('Docker Image Build') {
            steps{
                echo 'Docker Image Build'
                dir("${env.WORKSPACE}") {
            }
        }
        
        stage('SSH Publish') {
            steps {
                sshPublisher(publishers: [sshPublisherDesc(configName: '',
                transfers: [sshTransfer(cleanRemote: false, excludes: '',
                execCommand: '''fuser -k 8080/tcp
                export BUILD_ID=PetClinic
                nohup java -jar /home/ubuntu/deploy/spring-petclinic-3.4.0-SNAPSHOT.jar >> nohup.out 2>&1 &''', 
                execTimeout: 120000, 
                flatten: false, 
                makeEmptyDirs: false,
                noDefaultExcludes: false, 
                patternSeparator: '[, ]+', 
                remoteDirectory: '',
                remoteDirectorySDF: false, 
                removePrefix: 'target', 
                sourceFiles: 'target/*.jar')],
                usePromotionTimestamp: false,
                useWorkspaceInPromotion: false,
                verbose: false)])
            }
        }
    }
}
