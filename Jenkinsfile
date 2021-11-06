node
{
//Poll SCM - build history - artifact to keep
properties([buildDiscarder(logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '5', daysToKeepStr: '', numToKeepStr: '5')), pipelineTriggers([pollSCM('* * * * *')])])
    //defining variable for maven home dir
    def mavenHome = tool name: "maven.3.6.3"
//below stage will do source code checkout  from git repository
stage('CheckoutCodeFromGit')
{
git branch: 'development', credentialsId: '267d59e8-068a-4e33-b37e-dc9d61e8ad36', url: 'https://github.com/ommmahendra/maven-web-application.git'
}
//Below stage will do package build
stage('Build')
{
sh "${mavenHome}/bin/mvn clean package"
}
//Below stage will do Sonar qube report
stage('ExecuteSonarQubeReport')
{
sh "${mavenHome}/bin/mvn sonar:sonar"
}
//Below stage will upload the package to Nexus repos
stage('UploadArtifactIntoNexus')
{
sh "${mavenHome}/bin/mvn deploy"
}
//Below code will deploy the war file to tomcat server
stage('DeployApplicationIntoTomcatServer')
{
sshagent(['d8eb4fe2-73de-4ae1-a917-a274148e49b2'])
{
 sh "scp -o StrictHostKeyChecking=no target/maven-web-application.war ec2-user@65.0.27.198:/opt/tomcat952/webapps/"
}
}
//Below code will send mail
stage('SendEmailNotification')
{
mail bcc: 'omm.mahendra@gmail.com', body: '''Hello Pipe line build over

Regards
Jenkins''', cc: 'omm.mahendra@gmail.com', from: '', replyTo: '', subject: 'Pipeline Build Over', to: 'omm.mahendra@gmail.com'
}
}
