#Step 1 
Locust Installtion

run the following command to install Locust on ubuntu
'pip install locust'

#Step 2
Installing ZeroMQ

If you intend to run Locust distributed across multiple processes/machines, we recommend you to also install pyzmq:

'pip install pyzmq'

#Step 3
Running a sample program
Below is a quick little example of a simple locustfile.py:

from locust import HttpLocust, TaskSet

def index(l):
    l.client.get("/")

def profile(l):
    l.client.get("/sri")

class UserBehavior(TaskSet):
    tasks = {index: 2, profile: 1}

class WebsiteUser(HttpLocust):
    task_set = UserBehavior
    min_wait = 5000
    max_wait = 9000

#Step 4
Start Locust

To run Locust with the above Locust file, if it was named locustfile.py and located in the current working directory, we could run:

locust --host=http://localhost:4000
the example used to test is available in here https://github.com/LearningConatiner/Step1

#Step 5
Open up Locust’s web interface

Once you’ve started Locust using one of the above command lines, you should open up a browser and point it to 
http://127.0.0.1:8089 (if you are running Locust locally). 


