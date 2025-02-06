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
        docker.image('cdrx/pyinstaller-linux:python3').inside {  // Ganti ke Python 3
            // Copy file dari workspace Jenkins ke container
            sh 'cp -r sources /tmp/'
            sh 'mkdir -p dist'
            
            // Compile dalam directory yang konsisten
            sh 'pyinstaller --onefile /tmp/sources/add2vals.py'
            
            // Copy hasil build kembali ke workspace
            sh 'cp dist/add2vals ${WORKSPACE}/dist/'
        }
        archiveArtifacts 'dist/add2vals'
    }
}
