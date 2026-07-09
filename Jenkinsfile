pipeline {

    agent none

     environment{
        NETLIFY_SITE_ID = '5a9d7b10-3a84-4908-90bd-250b19865d04'
        NETLIFY_AUTH_TOKEN = credentials('netlify-token')
     }

    stages {

        stage('AWS'){
            agent{
                docker{
                    image 'amazon/aws-cli'
                    args"--entrypoint=''"
                }
            }
            steps{
                sh '''
                   aws --version
                   aws s3 ls

                '''
            }
        }


        stage('build') {

            agent{
                docker{
                    image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
                }
            }

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

           agent{
                        docker{
                            image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
                        }
                    }

            steps {
                echo '"Test stage"'
                sh'''
                   test -f build/index.html
                   npm test
                '''
            }
        }

        stage('E2E'){

           agent{
                        docker{
                            image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
                        }
                    }

            steps{
                sh'''
                    npm install serve
                    node_modules/.bin/serve -s build & sleep 10
                    npx playwright test --reporter=html
                '''
            }
        }


       stage('Deploy staging') {

          agent{
                       docker{
                           image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
                       }
                   }
           steps{
               sh '''
                   npm install netlify-cli@19.1.7 --no-save
                   node_modules/.bin/netlify --version
                   echo "프로젝트 스테이징 배포중  :   사이트 아이디  : $NETLIFY_SITE_ID"
                   node_modules/.bin/netlify status
                   node_modules/.bin/netlify deploy --dir=build
               '''
           }
       }


       stage('Deploy prod') {

          agent{
                       docker{
                           image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
                       }
                   }
           steps{
               sh '''
                   npm install netlify-cli@19.1.7 --no-save
                   node_modules/.bin/netlify --version
                   echo "프로젝트 배포중  :   사이트 아이디  : $NETLIFY_SITE_ID"
                   node_modules/.bin/netlify status
                   node_modules/.bin/netlify deploy --dir=build --prod
               '''
           }
       }

       stage('Prod E2E'){

          agent{
                       docker{
                           image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
                       }
                   }

            environment{
                CI_ENVIRONMENT_URL = 'https://peaceful-flan-e89814.netlify.app'
            }


            steps{
                sh '''
                      npx playwright test --reporter=html
                '''
            }
       }

    }

}
