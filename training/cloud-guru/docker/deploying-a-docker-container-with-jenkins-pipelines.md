# Deploying a Docker Container with Jenkins Pipelines

## Task 1: Configure Jenkins to run the new dockerized train-schedule pipeline

To accomplish this, you will need to do the following setup in Jenkins:

**Configure Jenkins credentials for the production server:**

<figure><img src="../../../.gitbook/assets/image (72).png" alt=""><figcaption></figcaption></figure>

Add the value to the browser. Finish install of Jenkins

<figure><img src="../../../.gitbook/assets/image (73).png" alt=""><figcaption></figcaption></figure>





**Using the `cloud_user` password, create a Jenkins credential called **_**deploy**_**.**

Dashboard -> Manage -> Jenkins Credentials

<figure><img src="../../../.gitbook/assets/image (74).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (75).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (76).png" alt=""><figcaption></figcaption></figure>

**Configure Jenkins credentials for the Docker image registry (Docker Hub).**

Dashboard -> Manage -> Jenkins Credentials

<figure><img src="../../../.gitbook/assets/image (77).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (78).png" alt=""><figcaption></figcaption></figure>

\<Make docker hub accounts>

1. Create a Jenkins credential called _docker\_hub\_login_. You will need a docker hub account in order to perform this step.
2. Configure a global property in Jenkins to store the production server IP.
3. Create a global property in Jenkins called _prod\_ip_ and set it to the _Production Server Public IP_ that appears on the learning activity page once the servers are started up.
4. Create a multibranch pipeline project in Jenkins called _train-schedule_.
5. Configure the Jenkins project to pull from your fork of the source code.
6. Make a fork of the Git repo at: [https://github.com/linuxacademy/cicd-pipeline-train-schedule-dockerdeploy](https://github.com/linuxacademy/cicd-pipeline-train-schedule-dockerdeploy)



## Task 2: Successfully deploy the train-schedule app to production as a Docker container using the Jenkins Pipeline

1. Implement the following new stages in the Jenkinsfile:

* `Build Docker Image`: Build a Docker image with the runnable code inside it.
* `Push Docker Image`: Push the image to Docker Hub.
* `DeployToProduction`: Deploy the new image to production.
* Stop and remove any older containers.
* Pull and run the newly built image.

1. After implementing these stages, run the build in Jenkins to deploy the application to production!

