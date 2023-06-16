---
layout: post
title:  "Change domain registrar from GoDaddy to Cloudflare"
date:   2023-06-16 10:16:57 -0500
categories: networking it administrative
tags: domain registrar godaddy cloudflare dns zone
---

## Overview

Just simply wanted to document my experience with switching my domain registrars from GoDaddy to CloudFlare.

## Why?

While investigating setting up DNSSEC for my recordsets, I realized that [Azure DNS doesn't currently support DNSSEC](https://feedback.azure.com/d365community/idea/d403899e-8526-ec11-b6e6-000d3a4f0789), and recommends using a 3rd party solution (like Cloudflare, Amazon Route 53, etc.)

Since I want to try out another Cloudflare offering as well, I decided to sign-up for an account and start moving all of my DNS Zones over there.

While I was in the process, since Cloudflare also acts as a registrar, I decided to transfer my domains as well. I don't use GoDaddy for anything else at this point, so one less account to think about going forward!

## Steps Taken

* Created new Cloudflare account
* Added new Site to Cloudflare

@root in records evaluates to root fullname
no A-records carried over - careful
make sure you have a billing profile setup
Must extend your domain(s) by 1-year when transferring registrars (wow, what a great customer experience...shouldn't GoDaddy have to give the difference to Cloudflare? I'm just the lowly customer I guess...)

## Timing

Just an approximation of times to expect for certain parts of this workflow. _(Note: I didn't do both domains in-parallel to reduce possible SNAFU radius)_

| Approximate Duration | Description |
|-|-|
| 10-minutes | Cloudflare to see new NS Records being pointed to |
| 30-minutes | Cloudflare to see that I had unlocked a domain |
| 45-minutes | Waiting for everything per-domain for full transfer |
| 4-5 days   | Waiting for GoDaddy to release domain _(this is normal)_ |

## Manually initiate zone scan

If you accidentally get into a position where Cloudflare's site won't scan your domain records again, you can easily call their REST APIs with [an API Key](https://dash.cloudflare.com/profile/api-tokens) like so:

```shell
curl --request POST \
     --url 'https://api.cloudflare.com/client/v4/zones/<your DNS zone's identifier>/dns_records/scan' \
     --header 'Content-Type: application/json' \
     --header 'Authorization: Bearer <your API key>'
```

You can find the DNS Zone Id on the same page that holds the Overview for your "sites".

## References

* https://developers.cloudflare.com/fundamentals/get-started/setup/add-site/
* https://developers.cloudflare.com/registrar/get-started/transfer-domain-to-cloudflare/
* https://www.godaddy.com/help/transfer-my-domain-away-from-godaddy-3560
* https://feedback.azure.com/d365community/idea/d403899e-8526-ec11-b6e6-000d3a4f0789
