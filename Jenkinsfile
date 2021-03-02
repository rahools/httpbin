podTemplate(label: 'jenkins-agent-pod', containers: [
    containerTemplate(name: 'docker', image: 'docker', ttyEnabled: true, command: 'cat'),
    containerTemplate(name: 'python', image: 'python', command: 'cat', ttyEnabled: true),
  ],
  volumes: [
    hostPathVolume(mountPath: '/var/run/docker.sock', hostPath: '/var/run/docker.sock'),
  ]) {
    node('jenkins-agent-pod') {

        environment {
            registry = "rahools/httpbin"
            registryCred = 'dockerhub'
            dockerImg = ''
        }

        // Gets the latest source code from the SCM
        stage('Clone Repository') {
            steps {
                checkout scm
            }
        }

        // Perform Unit Test
        stage('Unit Test') {
            container('python') {
                // Intsall deps
                sh 'sudo pip3 install pipenv'
                sh 'pipenv install --ignore-pipfile'

                // Run Tests
                sh 'pipenv run python test_httpbin.py'
            }
        }

        // Builds Docker Image
        stage('Build Image') {
            container('docker') {
                dockerImg = docker.build registry
                docker.withRegistry('', registryCred) {
                    dockerImg.push("$BUILD_ID")
                    dockerImg.push('latest')
                }
            }
        }
    }
}