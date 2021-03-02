podTemplate(label: 'jenkins-agent-pod', containers: [
    containerTemplate(name: 'docker', image: 'docker', ttyEnabled: true, command: 'cat'),
    containerTemplate(name: 'python', image: 'python', command: 'cat', ttyEnabled: true),
  ],
  volumes: [
    hostPathVolume(mountPath: '/var/run/docker.sock', hostPath: '/var/run/docker.sock'),
  ]) {
    node('jenkins-agent-pod') {

        // Gets the latest source code from the SCM
        checkout scm

        // Perform Unit Test
        stage('Unit Test') {
            container('python') {
                script {
                    // Intsall deps
                    sh 'pip3 install pipenv'
                    sh 'pipenv install --ignore-pipfile'

                    // Run Tests
                    sh 'pipenv run python test_httpbin.py'
                }
            }
        }

        // Builds Docker Image
        stage('Build Image') {
            container('docker') {
                script {
                    dockerImg = docker.build "rahools/httpbin"
                    docker.withRegistry('', 'dockerhub') {
                        dockerImg.push("$BUILD_ID")
                        dockerImg.push('latest')
                    }
                }
            }
        }
    }
}