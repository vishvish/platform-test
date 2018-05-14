pipeline {
  agent {
    label "jenkins-python"
  }
  environment {
    ORG         = 'vishv'
    APP_NAME    = 'builder-ansible'
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
        container('python') {
          sh "jx step validate --min-jx-version 1.2.36"
          sh "jx step post build --image \$JENKINS_X_DOCKER_REGISTRY_SERVICE_HOST:\$JENKINS_X_DOCKER_REGISTRY_SERVICE_PORT/$ORG/$APP_NAME:$PREVIEW_VERSION"
        }  
      }
    }
    /* stage('CI Build and push snapshot') { */
    /*     when { */
    /*         branch 'PR-*' */
    /*     } */
    /*     steps { */
    /*         container('jx-base') { */
    /*             sh "docker build -t docker.io/$ORG/$APP_NAME:SNAPSHOT-$BRANCH_NAME-$BUILD_NUMBER ." */
    /*             sh "docker push docker.io/$ORG/$APP_NAME:SNAPSHOT-$BRANCH_NAME-$BUILD_NUMBER" */
    /*         } */
    /*     } */
    /* } */

    /* stage('Build and Push Release') { */
    /*     when { */
    /*         branch 'master' */
    /*     } */
    /*     steps { */
    /*         container('jx-base') { */
    /*             sh "./jx/scripts/release.sh" */
    /*         } */
    /*     } */
    /* } */
  }
}
