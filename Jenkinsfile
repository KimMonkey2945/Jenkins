pipeline {
     agent{
            docker{
                image 'mcr.microsoft.com/playwright:v1.61.0-noble'
                reuseNode true
            }
       }

    stages {
        stage('build') {
            steps {
                sh'''
                    ls -la
                    node --version
                    npm --version
                    npm ci
                    npm run build
                    ls -la
                '''
            }
        }

        stage('test') {

            steps {
                echo '"Test stage"'
                sh'''
                   test -f build/index.html
                   npm test
                '''
            }
        }

        stage('E2E'){
            steps{
                sh'''
                    npm install serve
                    serve -s build &
                    sleep 10
                    npx playwright test
                '''
            }
        }
    }

    post {
        always{
            junit 'test-results/junit.xml'
        }
    }
}
