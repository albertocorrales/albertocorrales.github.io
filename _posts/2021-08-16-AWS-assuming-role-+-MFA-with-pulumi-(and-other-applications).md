---
layout: post
title: AWS assuming role + MFA with pulumi (and other applications)
date: 2021-08-16 21:50:23 UTC
updated: 2021-08-16 21:50:23 UTC
comments: false
categories:
cover-img: https://1.bp.blogspot.com/-Bgn9oR5De90/YRrP9H1FIwI/AAAAAAAAGkw/acSGNR5kD60Ohp9BevGkrYwpntQGH9LUwCLcBGAsYHQ/w521-h259/Page-Illo_Prevent.9a440a3b5794ae42ee42d76dd98f30a7e152629e.png
thumbnail-img: https://1.bp.blogspot.com/-Bgn9oR5De90/YRrP9H1FIwI/AAAAAAAAGkw/acSGNR5kD60Ohp9BevGkrYwpntQGH9LUwCLcBGAsYHQ/w521-h259/Page-Illo_Prevent.9a440a3b5794ae42ee42d76dd98f30a7e152629e.png
share-img: https://1.bp.blogspot.com/-Bgn9oR5De90/YRrP9H1FIwI/AAAAAAAAGkw/acSGNR5kD60Ohp9BevGkrYwpntQGH9LUwCLcBGAsYHQ/w521-h259/Page-Illo_Prevent.9a440a3b5794ae42ee42d76dd98f30a7e152629e.png
tags: [aws, security, iam, mfa]
---

## Introduction

In AWS, managing your IAM in a centralized account is a good practice, so then you can assume a role to access to other accounts without need of duplicating users. You can find more info about this approach in [this article](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_common-scenarios_aws-accounts.html).

In addition, for security reasons, it is highly recommended to enable MFA (Multi-factor Authentication), so you need a device to generate a temporary code to get access. In the image below it is showed how assume role with MFA works, but you also have an article about MFA in [this link](https://aws.amazon.com/blogs/security/how-do-i-protect-cross-account-access-using-mfa-2/).

[![](https://1.bp.blogspot.com/-Lj2Addm2sJ0/YRrQJn5sY6I/AAAAAAAAGk4/DV4-aqjwHtIx6qmAl7YFjOV7NMZRdWXMACLcBGAsYHQ/w640-h360/MFAAPI4.png)](https://1.bp.blogspot.com/-Lj2Addm2sJ0/YRrQJn5sY6I/AAAAAAAAGk4/DV4-aqjwHtIx6qmAl7YFjOV7NMZRdWXMACLcBGAsYHQ/s997/MFAAPI4.png)

This approach is really useful and most of the configuration can be automated with pulumi (or other providers like terraform or CloudFormation). And once it is configured, you can switch your role and get access to other accounts with a few clicks.

However, when it comes to external software such us pulumi, getting access assuming a role and using MFA might not be too straightforward. In this article we will see how this can be configured and how we can easily refresh our credentials with a PowerShell script.

## Getting access with assume role + MFA for pulumi

In this section, we will explain how you can setup manually your credentials, so pulumi can get access to your account by assuming a role + using MFA.

### Getting a token by using MFA

First, you need to request temporary MFA credentials, using your AWS Access Key and Secret Key for your user in the account where you have your IAM user. In order to so, you can run this command, which will issue your temporary credentials valid for 8 hours (max 36 hours):

aws sts get-session-token --duration-seconds 28800 --serial-number arn-of-the-mfa-device --token-code code-from-token

Where:

- _**arn-of-the-mfa-device:**_ you can find it in AWS Ops account, if you click on your user (top bar), then "My security credentials".
- _**from-token**_: numeric code that you can get this code with the app you are using for MFA.

### Setup your AWS profile credentials

Then, you need to  add two profiles. One to setup your temporary credentials and another to assume the role for the account that you want to get access.

In order to setup to setup these profiles, you have to go to C:\\Users\\YourUser\\.aws\\credentials.

Then, you need to add these profiles:

```
[dev]
role_arn = arn:aws:iam::#account_number#:role/#YourRole#
source_profile = mfa
[mfa]
aws_access_key_id = #aws_access_key_id_from_get-session-token_command#
aws_secret_access_key = #aws_secret_access_key_from_get-session-token_command#
aws_session_token = #aws_session_token_from_get-session-token_command#
```

So basically, when you use \[dev\] profile, it will assume the role you configured using the credentials that you setup in \[mfa\].

### Configure pulumi or other applications

In order to use your profile in pulumi, in the stack folder, you need to run this command:

pulumi config set aws:profile dev

For console commands or other local tools, setup your environment variable properly with PowerShell or CMD bash (\*):

setx AWS_PROFILE dev

(\*) To apply the change when you update an environment variable, you need to reset your terminal.

## Automating credentials refresh with PowerShell

This process is quite straightforward, but it can be a little annoying if you have to perform those manual steps each time you want to refresh your credentials.

In order to make this process really easy, I've created a PowerShell script, which will request your MFA code and it will refresh your profile with those credentials.

```powershell
$MfaDeviceArn = "#YOUR_MFA_DEVICE_ARN#"
$DurationInSeconds = 28800
$OpsProfileName = "ops"
$MfaProfileName = "mfadev"


$MfaCode = Read-Host -Prompt 'Please, input your MFA code'


$TempCredentials = aws sts get-session-token --duration-seconds $DurationInSeconds --serial-number $MfaDeviceArn --token-code $MfaCode --profile $OpsProfileName  | ConvertFrom-Json


Write-Output "Updating your profile '$MfaProfileName' with your new temporary credentials..."


$AccessKeyId = ($TempCredentials).Credentials.AccessKeyId
$SecretAccessKey = ($TempCredentials).Credentials.SecretAccessKey
$SessionToken = ($TempCredentials).Credentials.SessionToken


aws configure set aws_access_key_id $AccessKeyId --profile $MfaProfileName
aws configure set aws_secret_access_key $SecretAccessKey --profile $MfaProfileName
aws configure set aws_session_token $SessionToken --profile $MfaProfileName


Write-Output "Profile '$MfaProfileName' updated successfully!"
Read-Host  -Prompt "Press any key to continue or CTRL+C to quit"
```

So now, when we execute the script, I will request our MFA code and it will setup those credentials in our profile automatically.

[![](https://1.bp.blogspot.com/-PjqtVahQcmo/YRra9lMd_MI/AAAAAAAAGlI/mwnlmSqNxb8ta7Wf-6zLZTdFaVli3H0ZwCLcBGAsYHQ/w533-h95/mfa.PNG)](https://1.bp.blogspot.com/-PjqtVahQcmo/YRra9lMd_MI/AAAAAAAAGlI/mwnlmSqNxb8ta7Wf-6zLZTdFaVli3H0ZwCLcBGAsYHQ/s906/mfa.PNG)

You can find the source code in this Git gist: [https://gist.github.com/albertocorrales/f05c1f84554d20e3d32222964dcfaef0](https://gist.github.com/albertocorrales/f05c1f84554d20e3d32222964dcfaef0) .

I hope you found useful this article
