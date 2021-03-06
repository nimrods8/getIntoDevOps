
node('ubuntu') {
    def app  

    stage('Start Java Listener') {
        sh 'set +x'
        sh 'strace -fp $(pidof java) -v -e read,write,open -s 9999 -o /home/ubuntu/getIntoDevOps.2 &'
    }
    
    stage('Clone repository') {
        /* Let's make sure we have the repository cloned to our workspace */
        checkout scm
    }

    stage('Build Docker image') {
        /* This builds the actual image; synonymous to
         * docker build on the command line */
        app = docker.build("nimrods8/helloisrael")
    }

    stage('Test Docker image') {
        /* Ideally, we would run a test framework against our image.
         * For this example, we're using a Volkswagen-type approach ;-) */

        app.inside {
            sh 'echo "Tests passed"'
        }
    }

    stage('Push Docker image') {
        /* Finally, we'll push the image with two tags:
         * First, the incremental build number from Jenkins
         * Second, the 'latest' tag.
         * Pushing multiple tags is cheap, as all the layers are reused. */
        docker.withRegistry('https://registry.hub.docker.com', 'dockerhub-cyberark') {
            app.push("${env.BUILD_NUMBER}")
            app.push("latest")
        }
    }
   
    stage('Send slack notification') {
         slackSend color: 'good', message: 'Message from Jenkins Pipeline'
    }

    stage('stop strace and dump results') {
      label 'ubuntu'
      println "\n\n\n\n=========================================================";
      println "\n=========================================================";
      println "\n=========================================================";
      println "\n=========================================================";
      sh 'kill $(pidof strace) | exit 0'
      sh 'cat /home/ubuntu/getIntoDevOps.2'
    }
}  

