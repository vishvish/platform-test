pipeline {
  agent {
    label "jenkins-jx-base"
  }
  environment {
    ORG         = 'vishvish'
    APP_NAME    = 'platform-test'
    CHARTMUSEUM_CREDS = credentials('jenkins-x-chartmuseum')
  }
  stages {
    stage('Build') {
      environment {
        PREVIEW_VERSION = "0.0.0-SNAPSHOT-$BRANCH_NAME-$BUILD_NUMBER"
        PREVIEW_NAMESPACE = "$APP_NAME-$BRANCH_NAME".toLowerCase()
        HELM_RELEASE = "$PREVIEW_NAMESPACE".toLowerCase()
      }
      steps {
        container('jx-base') {
          sh 'export VERSION=$PREVIEW_VERSION && skaffold run -f skaffold.yaml'
          sh "jx step validate --min-jx-version 1.2.36"
          sh "jx step post build --image \$JENKINS_X_DOCKER_REGISTRY_SERVICE_HOST:\$JENKINS_X_DOCKER_REGISTRY_SERVICE_PORT/$ORG/$APP_NAME:$PREVIEW_VERSION"
            sh "echo \$(jx-release-version) > VERSION"
        }
        dir ('./charts/platform-test') {
          container('jx-base') {
            sh 'jx step changelog --version v\$(cat ../../VERSION)'
            // release the helm chart
            sh 'make release'
            // promote through all 'Auto' promotion Environments
            sh 'jx promote -b --all-auto --timeout 1h --version \$(cat ../../VERSION)'
          }
        }
      }
    }
  }
}
