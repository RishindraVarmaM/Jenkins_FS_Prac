pipeline {
    agent any

    environment {
        PATH = "/opt/homebrew/bin:${env.PATH}"
    }

    stages {

        // ===== FRONTEND BUILD =====
        stage('Build Frontend') {
            steps {
                dir('donationinventoryfrontend') {
                    sh 'npm install'
                    sh 'npm run build'
                }
            }
        }

        // ===== FRONTEND DEPLOY =====
        stage('Deploy Frontend to Tomcat') {
            steps {
                sh '''
                TOMCAT_PATH="/Users/rishi/apache-tomcat-10/webapps/donationfrontend"

                if [ -d "$TOMCAT_PATH" ]; then
                    rm -rf "$TOMCAT_PATH"
                fi

                mkdir "$TOMCAT_PATH"
                cp -R donationinventoryfrontend/dist/* "$TOMCAT_PATH"
                '''
            }
        }

        // ===== BACKEND BUILD =====
        stage('Build Backend') {
            steps {
                dir('donationinventorybackend') {
                    sh 'mvn clean package -DskipTests'
                }
            }
        }

        // ===== BACKEND DEPLOY =====
        stage('Deploy Backend to Tomcat') {
            steps {
                sh '''
                TOMCAT_WEBAPPS="/Users/rishi/apache-tomcat-10/webapps"

                # Remove old WAR and exploded folder
                rm -f "$TOMCAT_WEBAPPS/donationbackend.war"
                rm -rf "$TOMCAT_WEBAPPS/donationbackend"

                # Copy new WAR
                cp donationinventorybackend/target/*.war "$TOMCAT_WEBAPPS/"

                # Restart Tomcat
                /Users/rishi/apache-tomcat-10/bin/shutdown.sh
                /Users/rishi/apache-tomcat-10/bin/startup.sh
                '''
            }
        }

    }

    post {
        success {
            echo 'Deployment Successful!'
        }
        failure {
            echo 'Pipeline Failed.'
        }
    }
}
