node {

    def MavenHome = tool '3.9.9'

    // Build Started Notification
    slackSend(
        channel: '#jenkins_sre',
        color: '#FFFF00',
        message: """
🚀 BUILD STARTED

Job Name: ${env.JOB_NAME}
Build Number: ${env.BUILD_NUMBER}
Triggered By: ${currentBuild.getBuildCauses()[0].userId ?: 'SCM/Webhook'}
Build URL: ${env.BUILD_URL}
"""
    )

    try {

        stage('Git Checkout') {
            git branch: 'development',
            url: 'https://github.com/Pranay-Devops-online/Maven-web-app-project.git'
        }

        stage('Build') {
            sh "${MavenHome}/bin/mvn clean package"
        }

        stage('Sonar Report') {
            sh "${MavenHome}/bin/mvn sonar:sonar"
        }

        stage('Upload WAR to Nexus') {
            sh "${MavenHome}/bin/mvn deploy"
        }

        stage('Deploy to Tomcat') {
            sh "cp ${WORKSPACE}/target/*.war /opt/tomcat/webapps/"
        }

        // Success Notification
        slackSend(
            channel: '#jenkins_sre',
            color: 'good',
            message: """
✅ BUILD SUCCESS

Job Name: ${env.JOB_NAME}
Build Number: ${env.BUILD_NUMBER}
Status: SUCCESS
Build URL: ${env.BUILD_URL}
"""
        )

    } catch (Exception e) {

        // Failure Notification
        slackSend(
            channel: '#jenkins_sre',
            color: 'danger',
            message: """
❌ BUILD FAILED

Job Name: ${env.JOB_NAME}
Build Number: ${env.BUILD_NUMBER}
Status: FAILED
Failed Stage: ${env.STAGE_NAME}
Build URL: ${env.BUILD_URL}
"""
        )

        throw e
    }
}
