## Introduction

---

By now, you should know that we have a Jenkins server at our disposal.

Our Jenkins server is hosted on AWS, in a EC2 instance.

### Resources In Use

- EC2 Instance(t3.xlarge)
- Elastic IP
- Network Load Balancer
- Elastic Block Storage(100G)

### Structure

The instance is an Amazon Linux instance, based on RHEL, if you've used red hats before, this should
be similar.

Jenkins is installed as a yum package.

Jenkins itself does not handle SSL, so an Nginx reverse proxy is set up to handle SSL encryption.

And then it's TLS terminated by AWS, using a Network Load Balancer.
