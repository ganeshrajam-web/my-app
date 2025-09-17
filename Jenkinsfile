@Library('jenkins-shared-library@u/jwh/jfrog-testing') _
import com.tirerack.jenkins.configuration.PipelineConstants
import com.tirerack.jenkins.configuration.PipelineConfigFactory
import com.tirerack.jenkins.utils.ModuleNameUtils
import com.tirerack.jenkins.utils.ChangelogUtils
import com.tirerack.jenkins.configuration.SlackConfig
import com.tirerack.jenkins.steps.SlackNotifier
def project = [
    name    : 'test',
    gitUrl  : 'https://github.com/tirerack/my-app',
]
def scmConfig = PipelineConfigFactory.scmCheckoutConfig(project.gitUrl)
def libraryConfig = PipelineConfigFactory.libraryReleaseConfig()
pipeline {
  agent none
  options {
    buildDiscarder(logRotator(numToKeepStr: '10', artifactNumToKeepStr: '4'))
  }
  stages {
     stage('SCM Changelog') {
       agent { label PipelineConstants.AGENT_LABELS.PRIMARY }
      steps {
         runScmChangelog(scmConfig)
      }
    }
    stage('Release') {
      when {
        beforeAgent true
        branch develop
      }
      agent { label PipelineConstants.AGENT_LABELS.PODMAN }
      steps {
         script {
           def version = releaseLibraryModule("my-app", libraryConfig)
        }
      }
    }
  }
}
