node {
    def app

    stage('Clone repository') {
        /* Let's make sure we have the repository cloned to our workspace */

        checkout scm
        sh 'whoami'
    }

    stage('Build image') {
        /* This builds the actual image; synonymous to
         * docker build on the command line */
        sh 'whoami'
        app = docker.build("lhudson/helloworld")
    }

    stage('Test image') {
        /* Ideally, we would run a test framework against our image.
         * For this example, we're using a Volkswagen-type approach ;-) */

        app.inside {
            sh 'echo "Tests passed"'
        }
    }

    stage('Push image') {
        /* Now, we'll push the image with two tags:
         * First, the incremental build number from Jenkins
         * Second, the 'latest' tag.
         * Pushing multiple tags is cheap, as all the layers are reused. */
        docker.withRegistry('https://registry.hub.docker.com', 'docker-hub-credentials') {
            app.push("${env.BUILD_NUMBER}")
            app.push("latest")
        }
    }
    
    stage('Host image') {
        /* Finally, we'll host the image on our EC2 */
        
        /* This line removes any running container */
        sh "docker ps -a | awk '{ print \$1, \$2 }' | grep lhudson/helloworld | awk '{print \$1}' | xargs -I {} docker rm {} -f"

        sh 'docker run -d -p 80:80 lhudson/helloworld'
    }
}
