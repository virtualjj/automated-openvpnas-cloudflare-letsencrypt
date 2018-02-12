
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

Ideally, we'd use CloudFlare to protect the web admin UI since it is not possible to turn on MFA like you can on regular user accounts. Currently, after whitelisting the CloudFlare IPs, turning on CloudFlare protection breaks the web admin UI login. From a security perspective however, port 1194 UDP is open for the openvpn connections and cannot be proxied through CloudFlare anyway so the benefit of having CloudFlare protection is debatable. Using security groups to lock access from a source IP to the web admin UI *and* using a strong and unique username/password combination should be enough to mitigate most bots trying access the web admin console. VPC Flow Logging is enabled so feel free to see what is trying to access your OpenVPN AS instance.

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

Deployment is fairly straight forward.

Make sure that you are in the same region that you created your SSM secure sting parameters and click the **Launch Stack** button below:

[![Launch CloudFormation Stack](https://s3.amazonaws.com/cloudformation-examples/cloudformation-launch-stack.png
)](https://console.aws.amazon.com/cloudformation/home#/stacks/new?stackName=openvpnas-cf-letsencrypt&templateURL=https://s3-us-west-2.amazonaws.com/github-automated-openvpnas-cloudflare-letsencrypt/automated-openvpnas-cloudflare-letsencrypt.yml)

Enter the references to the SSM secure strings for your private CloudFlare account API data. Make sure that you don't mix up the Global API Key and Zone ID fields as it will fail and not create an A record in CloudFlare that points to your AWS EIP. When that happens, Let's Encrypt won't be able to resolve your FQDN and the certificate generation will fail.

<p align="center">
<img src="https://github.com/virtualjj/automated-openvpnas-cloudflare-letsencrypt/blob/master/images/readme/deploy-enter-ssm-params-cf-email.jpg" alt="Enter SSM secure string parameters and CloudFlare account email." height="75%" width="75%">
</p>

Enter an email address for the Let's Encrypt certificate registration. Try to use a different email address instead of your CloudFlare address.

Make sure to enter the correct FQDN&hellip;this will be the name of your OpenVPN server and URL that you'll use to connect to.

<p align="center">
<img src="https://github.com/virtualjj/automated-openvpnas-cloudflare-letsencrypt/blob/master/images/readme/deploy-email-fqdn-ec2key.jpg" alt="Enter Let's Encrypt email and FQDN" height="75%" width="75%">
</p>

Enter a source IP address for connecting to the OpenVPN AS web UI. You can leave it open to the world (i.e. 0.0.0.0/0) but we won't be using CloudFlare to protect this URL yet.

Enter a unique username for the web UI administrator account and a reference to the other SSM secure string for the secure password.

Click **Next** and follow the prompts to deploy the stack.

<p align="center">
<img src="https://github.com/virtualjj/automated-openvpnas-cloudflare-letsencrypt/blob/master/images/readme/deploy-lock-ip-secure-string-passwd.jpg" alt="Enter a source IP for connecting to the OpenVPN AS web UI and choose a unique username as well as a SSM secure string for the admin password."="75%" width="75%">
</p>

The stack, instance, CloudFlare DNS update, and Let's Encrypt certificate should be complete in about five minutes.

<p align="center">
<img src="https://github.com/virtualjj/automated-openvpnas-cloudflare-letsencrypt/blob/master/images/readme/deploy-success-outputs-url.jpg" alt="Successful stack deployment. Find the web UI URL in the Outputs section."="75%" width="75%">
</p>

If you look in your CloudFlare DNS panel, you should see a new A record added. Note that the CloudFlare icon is grey and not orange. This means that CloudFlare is only providing DNS services and not DDoS and other protections.

<p align="center">
<img src="https://github.com/virtualjj/automated-openvpnas-cloudflare-letsencrypt/blob/master/images/readme/deploy-new-a-record-cloudflare.jpg" alt="New A record in CloudFlare after stack deployment."="75%" width="75%">
</p>

Click on the URL listed in the CloudFormation Outputs section. Your OpenVPN AS should have a Let's Encrypt certificate installed and working now:

<p align="center">
<img src="https://github.com/virtualjj/automated-openvpnas-cloudflare-letsencrypt/blob/master/images/readme/deploy-le-certificate-sample.jpg" alt="Newly applied Let's Encrypt certificate on OpenVPN AS"="75%" width="75%">
</p>

When you login, make sure to enter the actual password that you set in the SSM secure string. At this point, the OpenVPN AS web UI doesn't know anything about the SSM secure string parameter.

<p align="center">
<img src="https://github.com/virtualjj/automated-openvpnas-cloudflare-letsencrypt/blob/master/images/readme/deploy-first-time-login-screen.jpg" alt="Login to OpenVPN AS with password, not SSM secure string ID"="75%" width="75%">
</p>

After successful login you'll have to agree to the EULA and then the status screen should appear. You can now configure your OpenVPN AS for use.

<p align="center">
<img src="https://github.com/virtualjj/automated-openvpnas-cloudflare-letsencrypt/blob/master/images/readme/deploy-login-success-status-screen.jpg" alt="Successful login to OpenVPN AS"="75%" width="75%">
</p>
