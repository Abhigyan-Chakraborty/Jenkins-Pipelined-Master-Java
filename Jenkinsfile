pipeline {
    agent any

    environment {
        // Adjust these if using a different location or setup
        MAVEN_HOME = tool 'Maven 3.9.1'     // Or your configured Maven tool name in Jenkins
        JAVA_HOME = tool 'JDK 21'           // Adjust based on Jenkins global config
        PATH = "${JAVA_HOME}/bin:${MAVEN_HOME}/bin:${env.PATH}"
    }

    stages {
        stage('Checkout') {
            steps {
                git url: 'https://your-git-repo/project.git', branch: 'main'
            }
        }

        stage('Build WAR') {
            steps {
                sh 'mvn clean package -DskipTests=false'
            }
        }

        stage('Run Tests') {
            steps {
                sh 'mvn test'
            }
        }

        stage('Deploy to Tomcat') {
            steps {
                script {
                    def warFile = findFiles(glob: '**/target/*.war')[0].path
                    def tomcatUrl = 'http://localhost:8082/manager/text/deploy' 
                    def contextPath = '/myapp'  // Update your desired path ('' = ROOT)

                    withCredentials([usernamePassword(credentialsId: 'tomcat-deployer-creds', usernameVariable: 'TOMCAT_USER', passwordVariable: 'TOMCAT_PASS')]) {
                        sh """
                            curl -v -u $TOMCAT_USER:$TOMCAT_PASS \
                            --upload-file ${warFile} \
                            "${tomcatUrl}?path=${contextPath}&update=true"
                        """
                    }
                }
            }
        }
    }

    post {
        success {
            echo '✅ Build, test, and deployment completed successfully.'
        }
        failure {
            echo '❌ Something went wrong.'
        }
    }
}
