node {
    checkout scm

    stage('Build') {
        docker.image('python:3-alpine').inside {
            sh 'python -m py_compile sources/add2vals.py sources/calc.py'
            stash includes: 'sources/*.py', name: 'compiled-results' 
        }
    }

    stage('Test') {
        docker.image('qnib/pytest').inside {
            // Specialized container for Python testing
            // Pre-installed with pytest framework and dependencies
            // Ready-to-use testing environment
            try {
                sh 'py.test --verbose --junit-xml test-reports/results.xml sources/test_calc.py'
            } finally {
                junit 'test-reports/results.xml'
            }
        }
    }

    stage('Deliver') {
         withEnv(['VOLUME=$(pwd)/sources:/src', 'IMAGE=cdrx/pyinstaller-linux:python3']) {
            dir(path: env.BUILD_ID) {
                unstash name: 'compiled-results'
                sh "docker run --rm -v ${VOLUME} ${IMAGE} 'pyinstaller -F add2vals.py'"
                archiveArtifacts "sources/dist/add2vals"
                // Deploy ke VM menggunakan SSH Key
            withCredentials([
                sshUserPrivateKey(
                    credentialsId: 'my-ssh-key', 
                    keyFileVariable: 'SSH_KEY', 
                    usernameVariable: 'VM_USER'
                )
            ]) {
                // Transfer file menggunakan SCP dengan SSH Key
                sh """
                    scp -i ${SSH_KEY} -o StrictHostKeyChecking=no \
                    sources/dist/add2vals \
                    ${VM_USER}@98.66.137.249:/idcamp/
                """
                
                // Optional: Jalankan perintah di remote VM
                sh """
                    ssh -i ${SSH_KEY} -o StrictHostKeyChecking=no \
                    ${VM_USER}@98.66.137.249 \
                    'chmod +x /idcamp/add2vals && \
                     /idcamp/add2vals'
                """
            }
            
            // Jeda 1 menit
            sleep 60
            
            // Bersihkan build
            sh "docker run --rm -v ${VOLUME} ${IMAGE} 'rm -rf build dist'"
            }
        }
    }
}