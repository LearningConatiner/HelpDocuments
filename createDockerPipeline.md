Step 1

Install docker plugin to build through Jenkins
  Go to Jenkins -> Manage Plugins -> Installed -> 
  Select 'Docker Plugin' and install 
  
 Step 2
 
  update jenkins file with following sections to build docker image and push the latest to registry with a tag latest.
   Below sample jenkinsfile builds the helloworld example and pushes to a local docker regtry running on 4000 port.
  
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

Step 3
execute docker ps in your local machine to check if the tagging has been done 
Below is the putput after executing the build from Jenkins , where helloworld has been built and tagges to the registry

root@sri-VirtualBox:~# docker ps -a
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                              NAMES
801ca5a61cb1        helloworld          "python hello.py"        50 minutes ago      Up 50 minutes       0.0.0.0:4000->4000/tcp, 8080/tcp   goofy_swirles
2f65a032affd        registry:2          "/entrypoint.sh /etc/"   47 hours ago        Up 25 hours         0.0.0.0:5000->5000/tcp     


Step 4

Below are the images list, the build no is used for tagging with the registry.
The latest build is also tagged as 'latest'.At any point in time , we can pull the older built images using the build number tag 

        registry
root@sri-VirtualBox:~# docker images
REPOSITORY                  TAG                 IMAGE ID            CREATED             SIZE
localhost:5000/helloworld   21                  178380ddbf07        About an hour ago   99.02 MB
localhost:5000/helloworld   22                  178380ddbf07        About an hour ago   99.02 MB
localhost:5000/helloworld   latest              178380ddbf07        About an hour ago   99.02 MB
helloworld                  latest              178380ddbf07        About an hour ago   99.02 MB
localhost:5000/helloworld   <none>              bbe3a7ade48f        3 days ago          99.02 MB
