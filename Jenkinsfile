pipeline {
  agent any
  stages {
    stage('build') {
      steps {
        echo 'Building'
        echo "My branch is: ${env.GIT_BRANCH}"
      }
      post {
        always {
          jiraSendBuildInfo()
        }
      }
    }
    stage("Test") {
      steps {
        echo "Deploying to test"
        echo 'Testing'
      }
    }
    stage('Request approval for Staging') { // Raise change request...
      steps {
        // input(message: 'Okay to Continue?', ok: 'Continue')
        echo 'Raise change request...'
        jiraSendDeploymentInfo(site:'demos-paraguay.atlassian.net',
          environmentId:'stg-1',
          environmentName:'stg-1',
          environmentType:'staging',
          state:"pending",
          enableGating:true, 
          serviceIds: [
            'b:YXJpOmNsb3VkOmdyYXBoOjpzZXJ2aWNlLzNlMjlhM2EwLTVkOGYtNGM5Yi1hM2ZmLTA2ODUwYTY5MTkyNS82MWEyNWMxYy03YmRiLTExZWUtYmE5MS0xMjhiNDI4MTk0MjQ='
          ]
        )
      }
    }
    stage("Approval gate for Staging") { // Check change request status ...
      steps {
        retry(20) { // Poll every 30s for 10min
          waitUntil { 
            sleep 30
            checkGatingStatus(
              site:'demos-paraguay.atlassian.net', 
              environmentId:'stg-1'
            )
          }
        }   
      }
    }
    stage("Deploy to Staging") {
      steps {
        echo 'Deploying to staging!!'
      }
      post {
        always {
          sh 'sleep 2'
        }
        // Notify Jira based on deployment step result
        success {
          jiraSendDeploymentInfo (
            site: 'demos-paraguay.atlassian.net',
            environmentId: 'stg-1',
            environmentName: 'stg-1',
            environmentType: 'staging',
            state: 'successful',
            serviceIds: [
              'b:YXJpOmNsb3VkOmdyYXBoOjpzZXJ2aWNlLzNlMjlhM2EwLTVkOGYtNGM5Yi1hM2ZmLTA2ODUwYTY5MTkyNS82MWEyNWMxYy03YmRiLTExZWUtYmE5MS0xMjhiNDI4MTk0MjQ='
            ]
          )
        }
      }
    }
    stage('Request approval for Production') { // Raise change request ...
      steps {
        echo 'Raise change request...'
        jiraSendDeploymentInfo(site:'demos-paraguay.atlassian.net',
          environmentId:'prod-1',
          environmentName:'prod-1',
          environmentType:'production',
          state:"pending",
          enableGating:true, 
            serviceIds: [
              'b:YXJpOmNsb3VkOmdyYXBoOjpzZXJ2aWNlLzNlMjlhM2EwLTVkOGYtNGM5Yi1hM2ZmLTA2ODUwYTY5MTkyNS82MWEyNWMxYy03YmRiLTExZWUtYmE5MS0xMjhiNDI4MTk0MjQ='
            ]
        )
      }
    }
    stage("Approval gate") { // Check change request status ...
      steps { 
        retry(20) { // Poll every 30s for 10min
          waitUntil { 
            sleep 30
            checkGatingStatus(
              site:'demos-paraguay.atlassian.net', 
              environmentId:'prod-1'
            )
          }
        }   
      }
    }
    stage("Deploy to Production") {
      steps {
        echo 'Deploying to production!!'
      }
      post {
        always {
          sh 'sleep 2'
        }
        // Notify Jira based on deployment step result
        success {
          jiraSendDeploymentInfo (
            site: 'demos-paraguay.atlassian.net',
            environmentId: 'prod-1',
            environmentName: 'prod-1',
            environmentType: 'production',
            state: 'successful',
            serviceIds: [
              'b:YXJpOmNsb3VkOmdyYXBoOjpzZXJ2aWNlLzNlMjlhM2EwLTVkOGYtNGM5Yi1hM2ZmLTA2ODUwYTY5MTkyNS82MWEyNWMxYy03YmRiLTExZWUtYmE5MS0xMjhiNDI4MTk0MjQ='
            ]
          )
        }
      }
    }
  }
}
