---
layout: post
title: Serving up your SSL Certificate from WebSphere 6.1 Application Server (no Web Server invovled)
date: 2009-02-10 00:00:00 -0500
categories: WebSphere
tags: application server, certificate, ibm, ikeyman, key store, ssl, unrestricted policy files, WebSphere
---
I recently had the task of getting one of our WebSphere 6.1 Application Servers to serve the SSL Certificate for our Demo server. Normally the recommended use of WebSphere is to put a Web Server in front of it like IIS, and then use the WebSphere Plug-in to route the requests to the Application Server, due to the fact that it handles better under heavy loads. Thus, you usually load the Certificate from your CA into IIS (or whatever Web Server you choose) and let it handle that job for you.

Login to the Integrated Solutions Console (ISC)

When you install WebSphere v6.1 with the default configuration options, you’ll get what’s called the Integrated Solutions Console (ISC). This allows easy management of your WebSphere instance and makes it much more feasible than using all of the command-line tools. To access the ISC, go to `http://<hostname>:9060/ibm/console` (<hostname> being the hostname of the server e.g. `localhost`)

Create a new Key store to hold your certificate

Now we can create a new Key store to hold your newly issued certificate. Expand the Security section on the left-hand navigation area and click on SSL certificate and key management. This will bring you to a page that has many options to play around with the way the Application Server manages its certificates, keystores, etc. Click on Key stores and certificates under the Related Items area. Click the New button next to Delete and Exchange Signers. For our example, let’s fill out the form with the following criteria…

Name: SSLKeyStore

Path: <where you want the keystore to reside on the local file system> (e.g.: ${CONFIG_ROOT}/cells/computerNameNode01Cell/nodes/computerNameNode01/SSLKeyStore.p12)

Password: <choose your own>

Confirm Password: <choose your own>

Type: PKCS12

Now click OK and then click the link for Save directly to the master configuration.

ikeyman, your new CA-signed Cert, and the “The specified database has been corrupted” message.

So I got a .pfx (PKCS12 Keystore) file from Thawte after they signed and generated my new Certificate and tried importing it into WebSphere via the Integrated Soluctions Console (ICS), but it kept telling me that either my password for the Keystore was invalid or that I had a corrupt Keystore. So I decided to try ikeyman, which ships with WebSphere, and all I got from it was “The specified database has been corrupted”…Well crap, was it really corrupt? I eventually found [this post](http://se9.blogspot.com/2008/04/x509-certificate-management-on-ibm-http.html), which helped me figure out what was causing that error…I needed the Unrestricted Policy Files loaded into the JRE.

Very useful resources

[IBM WebSphere Application Server V6.1 Security Handbook](http://www.redbooks.ibm.com/abstracts/sg246316.html?Open)
[WebSphere Security Fundamentals](http://www.redbooks.ibm.com/abstracts/redp3944.html?Open)
[IBM Unrestricted JCE Policy Files](https://www14.software.ibm.com/webapp/iwm/web/preLogin.do?source=jcesdk)
