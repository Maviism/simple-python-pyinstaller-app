node {
    stage('Build') {
        docker.image('python:2-alpine').inside {
            sh 'ls -l'
            sh 'python -m py_compile sources/add2vals.py sources/calc.py'
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
}