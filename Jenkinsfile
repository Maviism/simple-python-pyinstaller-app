node {
    checkout scm

    stage('Build') {
        // Use Python 3 container image
        docker.image('python:3-alpine').inside {
            // Compile Python files to check for syntax errors
            sh 'python -m py_compile sources/add2vals.py sources/calc.py'
        }
    }
    
    stage('Test') {
        docker.image('qnib/pytest').inside { 
            try {
                // Run tests using pytest
                sh 'py.test --verbose --junit-xml test-reports/results.xml sources/test_calc.py'
            } finally {
                // Archive the test results
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
                // tambahkan proses deploy disini
                // tambahkan proses jeda 1menit
                // sh "docker run --rm -v ${VOLUME} ${IMAGE} 'rm -rf build dist'"
            }
        }
    }
}
