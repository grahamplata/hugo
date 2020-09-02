---
title: "☁️ R and R accessories"
date: "2020-09-01"
author: grahamplata
excert: "Helpful snippets and solutions when working with R and it's many silly shortcomings"
tags: ["ops", "R", "aws", "cloud"]
---

## Table of Contents

Helpful snippets and solutions when working with R and it's many silly shortcomings

- [Problems and Scenarios](#problems-and-scenarios)
  - [301 Redirects when calling s3 from R](#301-redirects-when-calling-s3-from-r)
    - [Error](#error)
    - [Context](#context)
    - [Initial Troubleshooting](#initial-troubleshooting)
    - [Final Solution](#final-solution)
    - [Additional Thoughts](#additional-thoughts)

---

## Problems and Scenarios

### 301 Redirects when calling s3 from R

Stepping through issues encountered when using R and AWS. The following scenario happened when an analyst was trying to ship code to our RConnect instance. The error was only occuring when shipping to Rconnect.

#### Error

```log
[Connect] 2020/09/01 14:18:02.579556964 Additional data:
[Connect] Expression: 'parse_aws_s3_response(r, Sig, verbose = verbose)'
[Connect] Message: 'Moved Permanently (HTTP 301).'
[Connect] Found in the following log entry:
[Connect] 2020/09/01 14:18:02.579100596 Error in parse_aws_s3_response(r, Sig, verbose = verbose) :
```

#### Context

- RStudio and RConnect share the same base image and deployed on k8s
- RStudio and RConnect share the same pod pod iam role annotations
  - R is running on an EC2 instance so we want to expose role profile credentials via ec2metadata

#### Initial Troubleshooting

1. Check for issues [#176 Moved Permanently (HTTP 301)](https://github.com/cloudyr/aws.s3/issues/176) this was kinda similar but this user was not using the ec2 credentials that way.
2. Check I am able to access the ec2 layer on the Rconnect and Rstudio Pods with the python awscli. _(I was able to)_
3. Step through analyst's code with the Verbose Flag in the function set to TRUE

   ```R
   # RStudio
   s3HTTP(verb= "GET",bucket= 'my-production-bucket', verbose= TRUE)
   Locating credentials
   Checking for credentials in user-supplied values
   Checking for credentials in Environment Variables
   Checking for credentials in EC2 Instance Metadata
   Using EC2 Instance Metadata
   Using EC2 Instance Metadata for credentials
   Using user-supplied value for AWS Region ('us-west-2')
   S3 Request URL: https://s3-us-west-2.amazonaws.com/my-production-bucket/
   Executing request with AWS credentials
   Locating credentials
   Checking for credentials in user-supplied values
   Using user-supplied value for AWS Access Key ID
   Using user-supplied value for AWS Secret Access Key
   Using user-supplied value for AWS Session Token
   Using user-supplied value for AWS Region ('us-west-2')
   Parsing AWS API response
   Success: (200) OK

   # RConnect
   s3HTTP(verb= "GET",bucket= 'my-production-bucket', verbose= TRUE)
   Locating credentials
   Checking for credentials in user-supplied values
   Checking for credentials in Environment Variables
   Searching for credentials file(s)
   Attempting to read credentials from '.aws/credentials'
   No user-supplied credentials, environment variables, instance metadata, or credentials file found!
   Using user-supplied value for AWS Region ('us-west-2')
   S3 Request URL: https://s3-us-west-2.amazonaws.com/my-production-bucket/
   Executing request without AWS credentials
   Parsing AWS API response
   Client error: (403) Forbidden
   ```

   ```R
   # RConnect was missing these and defaulting to something else!
    Using EC2 Instance Metadata
    Using EC2 Instance Metadata for credentials
   ```

It seems that just installing the `aws.s3` library was not enough. The user also needed to add the `aws.ec2metadata` library so that it would pull the proper credentials.

#### Final Solution

This snippet below ended up solving the issue.

```R
# Install this package
install.packages("aws.ec2metadata", repos = c(cloudyr = "http://cloudyr.github.io/drat", getOption("repos")))
# import the library
library("aws.ec2metadata")
```

#### Additional Thoughts

- It may also be a good idea to use a virtual env like Renv so you don't rely on packages on the system.
- [awspack](https://github.com/cloudyr/awspack) is a bundle of all of the cloudyr project's packages for Amazon Web Services. In some instances it may be easier to have a single install instead of picking and choosing.
