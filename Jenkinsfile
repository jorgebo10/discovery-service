node {

    withMaven(maven:'maven') {

        stage('Commit') {
            git url: 'https://github.com/jorgebo10/discovery-service.git', credentialsId: 'github-jorgebo10', branch: 'master'
            sh 'mvn clean install'

            def pom = readMavenPom file:'pom.xml'
            print pom.version
            env.version = pom.version

            dir ('discovery-service') {
                def app = docker.build "localhost:5000/discovery-service:${env.version}"
                app.push()
            }
        }

        stage ('Stage') {
            docker.image("localhost:5000/discovery-service:${env.version}").run('-p 8761:8761 -h discovery --name discovery')
        }

        stage ('Final') {
            build job: 'account-service-pipeline', wait: false
        }
    }
}