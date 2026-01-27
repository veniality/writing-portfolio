---
title: AWS IAM IdC concepts
nav_order: 5
parent: Writing portfolio
layout: home
---

*[See the live version in the AWS CLI User Guide](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-sso-concepts.html).*

# AWS IAM Identity Center concepts for the AWS CLI
{: .no_toc }

This topic describes the key concepts of AWS IAM Identity Center (IAM Identity Center). IAM Identity Center is a cloud-based IAM service that simplifies user access management across multiple AWS accounts, applications, SDKs, and tools by integrating with existing identity providers (IdP). It enables secure single sign-on, permission management, and auditing through a centralized user portal, streamlining identity and access governance for organizations.

## Topics
{: .no_toc .text-delta }

- TOC
{:toc}


---

## What is IAM Identity Center

IAM Identity Center is a cloud-based identity and access management (IAM) service that enables you to centrally manage access to multiple AWS accounts and business applications.

It provides a user portal where authorized users can access the AWS accounts and applications they've been granted permission to, using their existing corporate credentials. This allows organizations to enforce consistent security policies and streamline user access management.

Regardless of which IdP you use, IAM Identity Center abstracts those distinctions away. For example, you can connect Microsoft Azure AD as described in the blog article [The Next Evolution in IAM Identity Center](https://aws.amazon.com/blogs/aws/the-next-evolution-in-aws-single-sign-on/).

{: .note-title }
> Note
>
> For information on using bearer auth, which uses no account ID and role, see [Setting up to use the AWS CLI with CodeCatalyst](https://docs.aws.amazon.com/codecatalyst/latest/userguide/set-up-cli.html) in the *Amazon CodeCatalyst User Guide*.


---

## Terms

Common terms when using IAM Identity Center are as follows:


<div class="code-example" markdown="1">
<dl>
<dt>Identity Provider (IdP)</dt>
<dd>An identity management system such as IAM Identity Center, Microsoft Azure AD, Okta, or your own corporate directory service.</dd>

<dt>AWS IAM Identity Center</dt>
<dd>IAM Identity Center is the AWS owned IdP service. Formerly known as AWS Single Sign-On, SDKs and tools keep the sso API namespaces for backward compatibility. For more information, see <a href="https://docs.aws.amazon.com/singlesignon/latest/userguide/what-is.html#renamed">IAM Identity Center rename</a> in the <i>AWS IAM Identity Center User Guide</i>.</dd>

<dt>AWS access portal URL, SSO start URL, Start URL</dt>
<dd>Your organization's unique IAM Identity Center URL to access your authorized AWS accounts, services, and resources.</dd>

<dt>Issuer URL</dt>
<dd>Your organization's unique IAM Identity Center issuer URL for programmatic access for your authorized AWS accounts, services, and resources. Starting with version 2.22.0 of the AWS CLI, issuer URL can be used interchangeably with the start URL.</dd>

<dt>Federation</dt>
<dd>The process of establishing trust between IAM Identity Center and an identity provider to enable single sign-on (SSO).</dd>

<dt>AWS accounts</dt>
<dd>The AWS accounts that you provide users access to through AWS IAM Identity Center.</dd>

<dt>Permission sets, AWS credentials, credentials, sigv4 credentials</dt>
<dd>Predefined collections of permissions that can be assigned to users or groups to grant access to AWS services.
</dd>

<dt>Registration scopes, access scopes, scopes</dt>
<dd>Scopes are a mechanism in OAuth 2.0 to limit an application's access to a user's account. An application can request one or more scopes, and the access token issued to the application is limited to the scopes granted. For information on scopes, see <a href="https://docs.aws.amazon.com/singlesignon/latest/userguide/customermanagedapps-saml2-oauth2.html#oidc-concept">Access scopes</a> in the *IAM Identity Center User Guide*.</dd>

<dt>Tokens, refresh token, access token</dt>
<dd>Tokens are temporary security credentials that are issued to you upon authentication. These tokens contain information about your identity and the permissions you've been granted.

When you access an AWS resource or application through the IAM Identity Center portal, your token is presented to AWS for authentication and authorization. This allows AWS to verify your identity and ensure you have the necessary permissions to perform your requested actions.

The authentication token is cached to disk under the `~/.aws/sso/cache` directory with a JSON filename based on the session name.</dd>

<dt>Session</dt>
<dd>An IAM Identity Center session refers to the period of time that a user is authenticated and authorized to access AWS resources or applications. When a user signs in to the IAM Identity Center portal, a session is established, and the user's token is valid for a specified duration. For more information on setting session durations, see <a href="https://docs.aws.amazon.com/singlesignon/latest/userguide/howtosessionduration.html">Set session duration</a> in the *AWS IAM Identity Center User Guide*.

During the session, you can navigate between different AWS accounts and applications without having to re-authenticate, as long as their session remains active. When the session expires, sign in again to renew your access.

IAM Identity Center sessions help to provide a seamless user experience while also enforcing security best practices by limiting the validity of user access credentials.</dd>

<dt>Authorization code grant with PKCE, PKCE, Proof Key for Code Exchange</dt>
<dd>Starting with version 2.22.0, Proof Key for Code Exchange (PKCE) is an OAuth 2.0 authentication grant flow for devices with a browser. PKCE is a simple and safe way to authenticate and obtain consent to access your AWS resources from desktops and mobile devices with web browsers. This is the default authorization behavior. For more information on PKCE, see <a href="https://docs.aws.amazon.com/singlesignon/latest/userguide/customermanagedapps-saml2-oauth2.html#auth-code-grant-pkce">Authorization Code Grant with PKCE</a> in the *AWS IAM Identity Center User Guide*.</dd>

<dt>Device authorization grant</dt>
<dd>An OAuth 2.0 authentication grant flow for devices with or without a web browser. For more information on setting session durations, see <a href="https://docs.aws.amazon.com/singlesignon/latest/userguide/customermanagedapps-saml2-oauth2.html#device-auth-grant">Device Authorization Grant</a> in the *AWS IAM Identity Center User Guide*.</dd>
</dl>
</div>


---

## How IAM Identity Center works

IAM Identity Center integrates with your organization's identity provider, such as IAM Identity Center, Microsoft Azure AD, or Okta. Users authenticate against this identity provider, and IAM Identity Center then maps those identities to the appropriate permissions and access within your AWS environment.

The following IAM Identity Center workflow assumes you have already configured your AWS CLI to use IAM Identity Center:

1. In your preferred terminal, run the `aws sso login` command.  
2. Sign in to your AWS access portal to start a new session.  
   * When you start a new session, you receive a refresh token and access token that is cached.  
   * If you already have an active session, the existing session is reused and expires when the existing session expires.  
3. Based on the profile you've set up in your `config` file, IAM Identity Center assumes the appropriate permission sets, granting access to the relevant AWS accounts and applications.  
4. The AWS CLI, SDKs, and Tools use your assumed IAM role to make calls to AWS services such as creating Amazon S3 buckets until that session expires.  
5. The access token from IAM Identity Center is checked hourly and is automatically refreshed using the refresh token.  
   * If the access token is expired, the SDK or tool uses the refresh token to get a new access token. These tokens' session durations are then compared, and if the refresh token is not expired IAM Identity Center provides a new access token.  
   * If the refresh token has expired, then no new access tokens are provided and your session has ended.  
6. Sessions end after refresh tokens expire, or when you manually log out using the `aws sso logout` command. Cached credentials are removed. To continue accessing services using IAM Identity Center, you must start a new session using the the `aws sso login` command.


---

## Additional resources

Additional resources are as follows.

* [Configuring IAM Identity Center authentication with the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-sso.html)  
* [Tutorial: Using IAM Identity Center to run Amazon S3 commands in the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-sso-tutorial.html)  
* [Installing or updating to the latest version of the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html)  
* [Configuration and credential file settings in the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-files.html)  
* [aws configure sso](https://docs.aws.amazon.com/cli/latest/reference/configure/sso.html) in the *AWS CLI version 2 Reference*  
* [aws configure sso-session](https://docs.aws.amazon.com/cli/latest/reference/configure/sso-session.html) in the *AWS CLI version 2 Reference*  
* [aws sso login](https://docs.aws.amazon.com/cli/latest/reference/sso/login.html) in the *AWS CLI version 2 Reference*  
* [aws sso logout](https://docs.aws.amazon.com/cli/latest/reference/sso/logout.html) in the *AWS CLI version 2 Reference*  
* [Setting up to use the AWS CLI with CodeCatalyst](https://docs.aws.amazon.com/codecatalyst/latest/userguide/set-up-cli.html) in the *Amazon CodeCatalyst User Guide*  
* [IAM Identity Center rename](https://docs.aws.amazon.com/singlesignon/latest/userguide/what-is.html#renamed) in the *AWS IAM Identity Center User Guide*  
* [OAuth 2.0 Access scopes](https://docs.aws.amazon.com/singlesignon/latest/userguide/customermanagedapps-saml2-oauth2.html#oidc-concept) in the *IAM Identity Center User Guide*  
* [Set session duration](https://docs.aws.amazon.com/singlesignon/latest/userguide/howtosessionduration.html) in the *AWS IAM Identity Center User Guide*  
* [Getting started tutorials](https://docs.aws.amazon.com/singlesignon/latest/userguide/tutorials.html) in the *IAM Identity Center User Guide*