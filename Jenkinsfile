pipeline {
     agent{
            docker{
                image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
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
                    node_modules/.bin/serve -s build & sleep 10
                    npx playwright test --reporter=html
                '''
            }
        }

       stage('Deploy') {
           steps{
               sh '''
                   npm install netlify-cli@19.1.7 --no-save
                   node_modules/.bin/netlify --version
               '''
           }
       }
    }

    post {
        always{
            junit 'jest-results/junit.xml'
        }
    }
}
