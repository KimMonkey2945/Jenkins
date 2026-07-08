pipeline {
     agent{
            docker{
                image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
                reuseNode true
            }
       }

     environment{
        NETLIFY_SITE_ID = '5a9d7b10-3a84-4908-90bd-250b19865d04'
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
                   echo "프로젝트 배포중  :   사이트 아이디  : $NETLIFY_SITE_ID "
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
