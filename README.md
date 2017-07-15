
# Automated Let's Encrypt OpenVPN AS (Access Server) With CloudFlare

This project automates the deployment of an OpenVPN AS Community Edition AWS instance with a Let's Encrypt certificate if CloudFlare is being used. In order for Let's Encrypt to issue an TLS certificate an active FQDN needs to be pre-configured which is a bit of a challenge when you are using automated (i.e. spontaneous) deployments.

## Supported Regions

All regions listed in the [Amazon Web Services EC2 Community Appliance Quick Start Guide](https://docs.openvpn.net/how-to-tutorialsguides/virtual-platforms/amazon-ec2-appliance-ami-quick-start-guide/) are supported.

## Caveats

While it's cool that you can deploy a Let's Encrypt TLS certificate for free this does not equate to more security. If anything it increases the attack surface but I digress. I do find it amazing that most folks won't blink an eye to pay for an $8 beer (or two, or three, or four, etc.) but will scoff at paying $8 for a one-year TLS certificate. With that said, combining Let's Encrypt with CloudFlare *is* interesting and could be considered a possible mitigation for the limitations of Let's Encrypt.

Finally, we could of course use Route 53 in place of CloudFlare but Route 53 is not *free* and does not include all of the additional *free* security perks of CloudFlare. However, this template could provide a nice basis for developing a Route 53 Let's Encrypt deployment. 

## Getting Started

### Preparation

