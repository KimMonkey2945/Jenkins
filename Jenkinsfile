pipeline {
     agent{
            docker{
                image 'node:18-alpine'
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
    }

    post {
        always{
            junit 'test-results/junit.xml'
        }
    }
}
