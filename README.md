
# AWS OPENVPN AS (ACCESS SERVER) WITH AUTOMATED LET'S ENCRYPT ON CLOUDFLARE DNS

This project automates the setup of an OpenVPN AS (Access Server) Community Edition on AWS with a Let's Encrypt certificate using CloudFlare DNS.

- [PURPOSE](#purpose)
- [SUPPORTED REGIONS](#supported-regions)
- [HOW TO CONTRIBUTE](#how-to-contribute)
- [CAVEATS](#caveats)
- [PREREQUISITES](#prerequisites)
- [DEPLOYMENT STEPS](#deployment-steps)


## PURPOSE

This project automates the deployment of an OpenVPN AS Community Edition AWS instance with a [Let's Encrypt](https://letsencrypt.org/) certificate in conjunction with [Cloudflare](https://www.cloudflare.com/) DNS. Accomplishing this task with AWS Route 53 is trivial; CloudFlare on the other hand requires some extra leg work and seemed like an interesting project.

## SUPPORTED REGIONS

All regions listed in the [Amazon Web Services EC2 Community Appliance Quick Start Guide](https://docs.openvpn.net/how-to-tutorialsguides/amazon-ec2-appliance-ami-quick-start-guide/) are supported.

## HOW TO CONTRIBUTE

Though I've tried to standardize as much as possible, this template will not meet everyones' requirements. I'm happy to receive suggestions and pull requests but please don't do a drive by, lobbing a bunch of issues for your special, unique use case. At the very least, please make sure you have read all of the documentation before raising issues.

## CAVEATS

While it's cool that you can deploy a Let's Encrypt TLS certificate for free, this does not equate to more or better security. This project is the result of a question - can I automate the deployment of OpenVPN AS and Let's Encrypt with CloudFlare? Steps have been taken to improve security by integrating EC2 SSM for secrets management for example, but if you require strong security in an hostile environment, this project is not for you.

## PREREQUISITES

Hopefully this is obvious but you'll need an AWS account and a CloudFlare account setup to manage DNS for your domain name to follow the steps in this README.md

## PREPARATION

In AWS, setup the following secure strings in AWS SSM Parameter store:

* CloudFlare Global API Key
* CloudFlare Zone ID for the target domain
* Password for the OpenVPN AS Web Admin UI administrator user

Make sure to select **Secure String** when you create one:
<p align="center">
<img src="https://github.com/virtualjj/automated-openvpnas-cloudflare-letsencrypt/blob/master/images/readme/prep-creating-secure-string.jpg" alt="Creating an EC2 SSM secure string example." height="75%" width="75%">
</p>

When you are done, you should have something similar to the screenshot below:
<p align="center">
<img src="https://github.com/virtualjj/automated-openvpnas-cloudflare-letsencrypt/blob/master/images/readme/prep-final-secure-strings-sample.jpg" alt="Sample AWS EC2 SSM secure strings." height="75%" width="75%">
</p>

Also, make sure you don't have any A records in your CloudFlare DNS that might conflict. This tutorial assumes a domain with no other DNS records:

<p align="center">
<img src="https://github.com/virtualjj/automated-openvpnas-cloudflare-letsencrypt/blob/master/images/readme/prep-cf-no-records-sample.jpg" alt="CloudFlare DNS with no records example." height="75%" width="75%">
</p>

## DEPLOYMENT STEPS
