
# AWS OPENVPN AS (ACCESS SERVER) WITH AUTOMATED LET'S ENCRYPT ON CLOUDFLARE

---
**August 9th, 2017 UPDATE:** This project is currently in progress and not finished. I'm testing having the AWS Elastic IP and Cloudflare A record automatically added to Cloudflare by either using AWS Lambda or CURL&mdash;both using the [Cloudflare API](https://api.cloudflare.com/#dns-records-for-a-zone-create-dns-record). The AWS Lambda will use Python and the [Cloudflare module](https://blog.cloudflare.com/python-cloudflare/) which is a wrapper for the Cloudflare API. It's rather trivial to do this with AWS Route 53 so might add functionality in the template to choose either Route 53 or Cloudflare -OR- keep the template completely separate. 
---

- [PURPOSE](#purpose)
- [SUPPORTED REGIONS](#supported-regions)
- [CAVEATS](#caveats)

## PURPOSE

This project automates the deployment of an OpenVPN AS Community Edition AWS instance with a [Let's Encrypt](https://letsencrypt.org/) certificate in conjunction with [Cloudflare](https://www.cloudflare.com/). In order for Let's Encrypt to issue an TLS certificate an active FQDN needs to be ***pre-configured*** which is a bit of a challenge when automating with CloudFormation because you won't know the EIP (Elastic IP) address until the resource is created.

## SUPPORTED REGIONS

All regions listed in the [Amazon Web Services EC2 Community Appliance Quick Start Guide](https://docs.openvpn.net/how-to-tutorialsguides/virtual-platforms/amazon-ec2-appliance-ami-quick-start-guide/) are supported.

## CAVEATS

While it's cool that you can deploy a Let's Encrypt TLS certificate for free this does not equate to more security. If anything it increases the attack surface but I digress. I do find it amazing that most folks won't blink an eye to pay for an $8 beer (or two, or three, or four, etc.) but will scoff at paying $8 for a one-year TLS certificate. With that said, combining Let's Encrypt with CloudFlare *is* interesting and could be considered a possible mitigation for the limitations of Let's Encrypt.

Finally, we could of course use Route 53 in place of CloudFlare but Route 53 is not *free* and does not include all of the additional *free* security perks of CloudFlare. However, this template could provide a nice basis for developing a Route 53 Let's Encrypt deployment.

More to come...
