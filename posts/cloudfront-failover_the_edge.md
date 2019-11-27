---
authors:
- Matt Reed
tags:
- AWS
- CloudFront
- Origin Groups
date: 2019-5-07T00:00:00.000Z
title: "Lambda@Edge Functions in Conjunction with CloudFront Origin Groups"
image: https://raw.githubusercontent.com/ippontech/blog-usa/master/images/2019/11/cloudfront_failover.png
---

## Life On The Edge

CloudFront allows for one or more Lambda@Edge Functions to be [executed in between fetches](https://docs.aws.amazon.com/lambda/latest/dg/lambda-edge.html) to the Origin. Why Lambda@Edge Functions? For those familiar with the more common Serverless Lambda Functions, the basic premise of Lambda@Edge Functions is similar however they are run on [much less computationally capable](https://docs.aws.amazon.com/en_pv/AmazonCloudFront/latest/DeveloperGuide/cloudfront-limits.html#limits-lambda-at-edge) servers in more diverse locations. This is so that they exist closer to the end user to decrease load time on their side. What Lambda@Edge Functions primarily are purposed for is minor customizations of the fetched content during delivery. Amazon docs even show [quite a few example functions for popular topics](https://docs.aws.amazon.com/en_pv/AmazonCloudFront/latest/DeveloperGuide/lambda-examples.html).

## Failover The Edge

Different Lambda@Edge Functions are allowed to be executed on request and response events. So how does this work in conjunction with Origin Failover? If the Primary Origin returns an error status code, keep in mind it will pass through the response's Lambda@Edge before the request is re-tried with the Secondary Origin. If there is any custom error handling in the response's Lambda@Edge this could interfere with the built-in failover functionality.
![Origin Failover with Lambda@Edge Functions on origin request and response events](https://docs.aws.amazon.com/en_pv/AmazonCloudFront/latest/DeveloperGuide/images/origingroups-with-lambda-edge.png)

## Adding Headers

Typically whenever a user navigates a modern website, the response comes back with a header called the ["Content-Security-Policy" (CSP)](https://content-security-policy.com/). The reason for that is because most frameworks need to load various styles and scripts from a whole host of different sources. As [Mozzila's spec](https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP) states, "A CSP compatible browser will then only execute scripts loaded in source files received from those allowlisted domains..." For those unfamiliar, here is an example of a CSP header (take a guess which site it comes from):

`Content-Security-Policy: default-src * data: blob: 'self';script-src *.facebook.com *.fbcdn.net *.facebook.net *.google-analytics.com *.virtualearth.net *.google.com 127.0.0.1:* *.spotilocal.com:* 'unsafe-inline' 'unsafe-eval' blob: data: 'self';style-src data: blob: 'unsafe-inline' *;connect-src *.facebook.com facebook.com *.fbcdn.net *.facebook.net *.spotilocal.com:* wss://*.facebook.com:* https://fb.scanandcleanlocal.com:* attachment.fbsbx.com ws://localhost:* blob: *.cdninstagram.com 'self';`

Intended functionality using this example will send the user the CSP header by overriding the response in a specified Lambda@Edge Function like so:

    exports.handler = (event, context, callback) => {
        const response = event.Records[0].cf.response;
        const headers = response.headers;

        const  = 'Content-Security-Policy';
        const headerCspValue = "default-src * data: blob: 'self';script-src *.facebook.com *.fbcdn.net *.facebook.net *.google-analytics.com *.virtualearth.net *.google.com 127.0.0.1:* *.spotilocal.com:* 'unsafe-inline' 'unsafe-eval' blob: data: 'self';style-src data: blob: 'unsafe-inline' *;connect-src *.facebook.com facebook.com *.fbcdn.net *.facebook.net *.spotilocal.com:* wss://*.facebook.com:* https://fb.scanandcleanlocal.com:* attachment.fbsbx.com ws://localhost:* blob: *.cdninstagram.com 'self';`";

        headers[headerCsp.toLowerCase()] = [{key: headerCsp, value: headerCspValue}]; 

        callback(null, response);
    };

For more information, see [Adding HTTP Security Headers Using Lambda@Edge and Amazon CloudFront](https://aws.amazon.com/blogs/networking-and-content-delivery/adding-http-security-headers-using-lambdaedge-and-amazon-cloudfront/).

<u>Sources</u>:

* [Triggering Lambda@Edge Functions execution in CloudFront](https://docs.aws.amazon.com/en_pv/AmazonCloudFront/latest/DeveloperGuide/lambda-edge-add-triggers.html)
* [Lambda@Edge Limits](https://docs.aws.amazon.com/en_pv/AmazonCloudFront/latest/DeveloperGuide/cloudfront-limits.html#limits-lambda-at-edge)
* [Only semi-recently was support introduced for writing Lambda@Edge functions in Node.js v8.10](https://aws.amazon.com/about-aws/whats-new/2018/05/lambda-at-edge-adds-support-for-node-js-v8-10/)
* [Lambda@Edge Example Functions for Popular Topics](https://docs.aws.amazon.com/en_pv/AmazonCloudFront/latest/DeveloperGuide/lambda-examples.html)
* [Content-Security-Policy Header](https://content-security-policy.com/)
* [Mozilla Content Security Policy (CSP)](https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP)
* [Adding HTTP Security Headers Using Lambda@Edge and Amazon CloudFront](https://aws.amazon.com/blogs/networking-and-content-delivery/adding-http-security-headers-using-lambdaedge-and-amazon-cloudfront/)