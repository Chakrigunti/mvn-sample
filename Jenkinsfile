pipeline {
    agent any

    environment {
        DEPLOY_USER = 'ubuntu' 
        DEPLOY_HOST = 'your.ec2.public.ip'
        PEM_FILE = 'test.pem'                       
        APP_DIR = '/home/ubuntu/app'                     
        JAR_NAME = 'target/java-jar.jar'                
	}

    stages {
        stage('Clone Repository') {
            steps {
                git 'https://github.com/Chakrigunti/mvn-sample.git'
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                chmod 400 $PEM_FILE
                ssh -o StrictHostKeyChecking=no -i $PEM_FILE $DEPLOY_USER@$DEPLOY_HOST << EOF
                  pkill -f 'java' || true
                  mkdir -p $APP_DIR
                EOF

                scp -o StrictHostKeyChecking=no -i $PEM_FILE $JAR_NAME $DEPLOY_USER@$DEPLOY_HOST:$APP_DIR/app.jar

                ssh -i $PEM_FILE $DEPLOY_USER@$DEPLOY_HOST << EOF
                  cd $APP_DIR
                  nohup java -jar app.jar > output.log 2>&1 &
                EOF
                '''
            }
        }
    }

    post {
        success {
            echo 'Deployment completed successfully.'
        }
        failure {
            echo 'Deployment failed.'
        }
    }
}

