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
        docker.image('python:3-alpine').inside {
            try {
                // Instal dependensi yang diperlukan
                sh 'apk add --no-cache build-base python3-dev'
                
                // Instal PyInstaller
                sh 'pip install pyinstaller'
                
                // Buat executable dengan PyInstaller
                sh 'pyinstaller --onefile sources/add2vals.py'
                
                // Arsipkan executable
                archiveArtifacts artifacts: 'dist/add2vals', fingerprint: true
            } catch (err) {
                echo "Error in Deliver stage: ${err}"
                throw err
            }
        }
    }
}
