# Instructions for Setup

Create an EC2 instance ( Ubuntu) and install jenkins on it.

Configure awscli

Install Docker, Lint, Amazon EKS binaries

Create Kubernetes cluster using aws EKS 

Create github repository for the project

Configure jenkins with credentials for github, aws and docker

Write a docker file to build the nginix image with customised index.html

Write jenkins pipeline for lint,build and push docker image to docker hub

Add jenkins pipeline stage for deploying customised docker conatainer to the kubernetes cluster created.

Expose the service to a load balance.
