pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                // Checks the code from the GitHub repository
                checkout scm
            }
        }

        stage('Build') {
            steps {
                // Builds the Spring Boot application using Maven
                sh 'mvn clean package'
            }
        }

        stage('Test') {
            steps {
                // Run tests
                sh 'mvn test'
            }
        }

        stage('Deploy') {
            steps {
                // SCP the JAR file to the FreeBSD server
                withCredentials([usernamePassword(credentialsId: 'remote', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                    sh '''
                        scp target/myapp.jar ${USERNAME}@freebsd_server_ip:/path/to/app/directory
                    '''
                }
            }
        }

        stage('Start App in Jail') {
            steps {
                // SSHs into the FreeBSD server and starts the app inside a jail.
                script {
                    def remote = [:]
                    remote.name = 'freebsd_server'
                    remote.host = 'freebsd_server_ip'
                    remote.user = 'username'
                    remote.password = 'password'
                    remote.allowAnyHosts = true

                    stage('SSH') {
                        sshCommand remote: remote, command: """
                            jexec [jail_name] sh -c 'nohup java -jar /path/to/app/directory/myapp.jar > /dev/null 2>&1 &'
                        """
                    }
                }
            }
        }
    }
}
