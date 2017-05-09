Step 1

Install docker plugin to build through Jenkins
  Go to Jenkins -> Manage Plugins -> Installed -> 
  Select 'Docker Plugin' and install 
  
 Step 2
 
  update jenkins file with following sections to build docker image and push the latest to registry with a tag latest
  Below sample jenkinsfile build the helloworld example and pushes to a local docker regtry running on 4000 port.
  
  node {
    def app

    stage('Clone repository') {
        /* Let's make sure we have the repository cloned to our workspace */

        checkout scm
    }

    stage('Build image') {
        /* This builds the actual image; synonymous to
         * docker build on the command line */

        app = docker.build("helloworld")
    }

    stage('Test image') {
        /* Ideally, we would run a test framework against our image.
         * For this example, we're using a Volkswagen-type approach ;-) */

        app.inside {
            sh 'echo "Tests passed"'
        }
    }
       stage('Push image') {
        /* Finally, we'll push the image with two tags:
         * First, the incremental build number from Jenkins
         * Second, the 'latest' tag.
         * Pushing multiple tags is cheap, as all the layers are reused. */
        docker.withRegistry('https://localhost:5000/registry2', '') {
            app.push("${env.BUILD_NUMBER}")
            app.push("latest")
        }
    }
}
