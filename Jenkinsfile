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
        checkout scm
        docker.image('python:3-alpine').inside {
            // Install PyInstaller for Python 3
            sh 'pip install --no-cache-dir pyinstaller'

            // Compile the Python script into a standalone executable
            sh 'pyinstaller --onefile sources/add2vals.py'

            // Archive the output executable
            archiveArtifacts 'dist/add2vals'
        }
    }
}
