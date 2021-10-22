// Agent labels
def zOsAgentLabel = env.ZOS_AGENT_LABEL ? env.ZOS_AGENT_LABEL : 'zos-agent'
def linuxAgent = 'master'

// GIT repositories
def srcGitRepo =  null
def srcGitBranch = null
def zAppBuildGitRepo = 'https://github.com/IBM/dbb-zappbuild.git'
def zAppBuildGitBranch = 'development' // Some important issues are not yet merged into master..
def dbbGitRepo = 'https://github.com/IBM/dbb.git'
def dbbGitBranch = 'master'

// DBB
def dbbHome=null
def dbbUrl='https://52.117.174.14:32455/dbb/'
def dbbHlq=null
def dbbBuildType='-f'
def dbbGroovyzOpts= ''
def dbbBuildExtraOpts= ''
def dbbCredentialOptions='-id ADMIN -pw ADMIN'

// Artifactory
def artiCredentialsId = 'admin'


// UCD
def ucdApplication = 'GenApp-DeployMaria'
def ucdProcess = 'Deploy'
def ucdComponent = 'GenAppCompMari'
def ucdEnv = 'Development'
def ucdSite = 'UrbanCodeE2EPipeline'

// Verbose
def verbose = false

// Private
def hasBuildFiles = true
def idzCodeReviewFlag = false
def buildVerbose = ''

@NonCPS
String getArtifactoruUrl(String artiUrl) {
    // UCD always add /artifactory
    def exp =  /(.*)\\/artifactory?$/
    def match = artiUrl =~ exp
    if (match.find()) {
        return match.group(1)
    }
    return artiUrl
}


pipeline {

    agent { label linuxAgent }

    options { skipDefaultCheckout(true) }

    stages {
        
        stage('Git Clone/Refresh') {
            agent { label zOsAgentLabel }
            steps {
                script {
                    // Verbose
                    verbose = env.VERBOSE && env.VERBOSE == 'true' ? true : false
                    buildVerbose = verbose ? '-v' : ''
                    if ( verbose ) {
                        echo sh(script: 'env|sort', returnStdout: true)
                    }
                                     
                    dir('genapp') {
                        sh(script: 'rm -f .git/info/sparse-checkout', returnStdout: true)
                        srcGitRepo = scm.getUserRemoteConfigs()[0].getUrl()
                        srcGitBranch = scm.branches[0].name
                        def scmVars = null
                        scmVars = checkout([$class: 'GitSCM', branches: [[name: srcGitBranch]],
                                                doGenerateSubmoduleConfigurations: false,
                                                extensions: [
                                                [$class: 'SparseCheckoutPaths',
                                                   sparseCheckoutPaths:[[$class:'SparseCheckoutPath', 
                                                   path:'genapp/']]]
                                                ],
                                                submoduleCfg: [],
                                                userRemoteConfigs: [[
                                                                     url: srcGitRepo,
                                                                     ]]])
                    }
                    
                    dir("dbb-zappbuild") {
                        sh(script: 'rm -f .git/info/sparse-checkout', returnStdout: true)
                        def scmVars =
                            checkout([$class: 'GitSCM', branches: [[name: zAppBuildGitBranch]],
                              doGenerateSubmoduleConfigurations: false,
                              submoduleCfg: [],
                            userRemoteConfigs: [[
                                url: zAppBuildGitRepo,
                            ]]])
                    }
                    
                    dir("dbb") {
                        sh(script: 'rm -f .git/info/sparse-checkout', returnStdout: true)
                        def scmVars =
                            checkout([$class: 'GitSCM', branches: [[name: dbbGitBranch]],
                              doGenerateSubmoduleConfigurations: false,
                              extensions: [
                                       [$class: 'SparseCheckoutPaths',  sparseCheckoutPaths:[
                                          [$class:'SparseCheckoutPath', path:'Pipeline/CreateUCDComponentVersion/'],
                                          [$class:'SparseCheckoutPath', path:'Pipeline/RunIDZCodeReview/']
                                       ]]
                                    ],
                              submoduleCfg: [],
                            userRemoteConfigs: [[
                                url: dbbGitRepo,
                            ]]])
                    }
                }
            }
        }

       stage('DBB Build') {
			steps {
				script{
					node(zOsAgentLabel ) {
						sh "/usr/lpp/IBM/dbb/bin/groovyz /u/ibmuser/projects/dbb-zappbuild/build.groovy --logEncoding UTF-8 -w ${WORKSPACE} --application genapp --sourceDir ${WORKSPACE}  --workDir ${WORKSPACE}/BUILD-${BUILD_NUMBER}  --hlq IBMUSER.GENAPP --url $dbbUrl -pw ADMIN $dbbBuildType"						
 					}
				}
			}
			post {
				always {
					node( zOsAgentLabel ) {
						dir("${WORKSPACE}/BUILD-${BUILD_NUMBER}") {
							archiveArtifacts allowEmptyArchive: true,
											artifacts: '*.log,*.json,*.html,*.vtptc',
											excludes: '*clist',
											onlyIfSuccessful: false
						}
					}
				}
			}
		}
    }
}