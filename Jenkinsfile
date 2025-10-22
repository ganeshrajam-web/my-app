pipeline {
  agent any
  options {
    buildDiscarder(logRotator(numToKeepStr: '10', artifactNumToKeepStr: '4'))
  }
  stages {
    stage('Release') {
      when {
        beforeAgent true
        not { changelog '.*maven-release-plugin.*' }
      }
      tools {
        jfrog 'jfrog-cli-latest'
      }
      environment {
        JFROG_CLI_LOG_LEVEL="DEBUG"
      }
      steps {
        sh 'git tag | xargs git tag -d'
        withMaven(jdk: 'openjdk-21', maven: 'default', mavenSettingsConfig: 'jfrog-maven-settings', traceability: true) {
          jf "mvnc --repo-deploy-snapshots maven-snapshot --repo-deploy-releases maven-release --repo-resolve-snapshots maven-snapshot --repo-resolve-releases maven-release"
          jf "mvn clean package"
          jf "mvn -B release:prepare -Dresume=false -DpushChanges=false"
          sh "mvn release:perform -DlocalCheckout=true"
        }
      }
    }
  }
}
