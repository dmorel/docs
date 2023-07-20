# Introduction to AWS Identity and Access Management

Amazon Web Services

https://www.coursera.org/learn/introduction-to-aws-identity-and-access-management/

## Introduction to Identity and Access Management

A user must be authenticated, by signing in to AWS using their credentials, in order to send a request to AWS. Some services allow a few requests from anonymous users. However, they are the exception to the rule.

AWS requires both authentication and authorization in order to make a request. You must be signed in **and** you must have the necessary permissions to perform an action. 

https://docs.aws.amazon.com/IAM/latest/UserGuide/intro-structure.html#intro-structure-authentication

https://docs.aws.amazon.com/IAM/latest/UserGuide/intro-structure.html#intro-structure-authorizationKey 

## Key concepts to AWS Identity and Access Management

Understanding core concepts, such as users and groups, is an important part of building your foundational knowledge of AWS Identity and Access Management. 

This link is a great place to get started in diving into the IAM identities, and learning about how they can be utilized. 

https://docs.aws.amazon.com/IAM/latest/UserGuide/id.html

Effect, Action, and Resource make up both the required and the most basic parts of an AWS IAM policy. They provide a great start in the granular control you have within IAM policies. 

https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements_effect.html

https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements_action.html

https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements_resource.html

An IAM role is an IAM entity that defines a set of permissions for making AWS service requests. IAM roles are not associated with a specific user or group. Instead, trusted entities assume roles, such as IAM users, applications, or AWS services such as EC2.

IAM roles allow you to delegate access with defined permissions to trusted entities without having to share long-term access keys. You can use IAM roles to delegate access to IAM users managed within your account, to IAM users under a different AWS account, or to an AWS service such as EC2.

https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles.html

Identity federation provides a method to connect existing organization identities to AWS permissions. While this course will not focus on this access management method, this link will provide you with a starting point if you want to look further into the topic. 

https://aws.amazon.com/identity/federation/

As I discussed, the CLI is one of the easiest and most direct ways to work with the API calls within AWS. While we won’t be focusing on this a lot during the course, it could be helpful for you to become more comfortable with the AWS Command Line Interface as you continue your learning on AWS.

 https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-welcome.html

Amazon Elastic Compute Cloud (Amazon EC2) provides scalable computing capacity in the Amazon Web Services (AWS) cloud. Using Amazon EC2 eliminates your need to invest in hardware up front, so you can develop and deploy applications faster. 

You can use Amazon EC2 to launch as many or as few virtual servers as you need, configure security and networking, and manage storage. Amazon EC2 enables you to scale up or down to handle changes in requirements or spikes in popularity, reducing your need to forecast traffic.

https://aws.amazon.com/ec2/faqs/

Amazon Simple Storage Service, or Amazon S3, is a very powerful and versatile storage tool available to you in AWS. This link will provide further information about the concepts discussed in the video, as well as others that were not covered. 

https://docs.aws.amazon.com/AmazonS3/latest/dev/Introduction.html#CoreConcepts

## Introduction to Access Management

Access management is the core focus of our course. While it was introduced as a concept in the video, this is a concept important to understand when learning about the utilization of AWS Identity and Access Management. 

https://docs.aws.amazon.com/IAM/latest/UserGuide/access.html

The resource component of a policy is how you specify *what* you are controlling access to. Learning how to precisely identify a resource in your policies will provide you with a lot of the control you’re seeking through access management.

https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_actions-resources-contextkeys.html

https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements_resource.html

https://docs.aws.amazon.com/general/latest/gr/aws-arns-and-namespaces.html#genref-aws-service-namesspaces

https://docs.aws.amazon.com/IAM/latest/UserGuide/access_tags.html

https://docs.aws.amazon.com/general/latest/gr/rande.html#regional-endpoints

The feature of tagging provides you with customizable metadata that assists in organization, cost allocation, access management, and more. Make sure you look into tagging and what it can do for you. 

Additionally, in the video I mentioned AWS Organizations and AWS Resource Groups. I have provided some additional information for you here in order to look further into those AWS resources.  

https://docs.aws.amazon.com/IAM/latest/UserGuide/id_tags.html

https://docs.aws.amazon.com/general/latest/gr/aws_tagging.html

https://docs.aws.amazon.com/IAM/latest/UserGuide/introduction_attribute-based-access-control.html

https://docs.aws.amazon.com/organizations/latest/userguide/orgs_introduction.html

https://docs.aws.amazon.com/ARG/latest/userguide/welcome.html

## Understanding Policies

In an AWS Identity and Access Management policy, the principal and condition elements aren’t always utilized, but they provide even more control through your policies. They go further to control who has access, as well as how access can be granted. 

https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements_principal.html

https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements_condition.html

You should consider using IAM roles over long-term credentials whenever possible. IAM roles are especially important in the following scenarios: 

- Providing access to services like Amazon EC2 that need access to other AWS services.
- Providing access to users in other AWS accounts access to resources in your AWS account. 
- Providing access to identities that are stored externally through a corporate identity provider or a social media website. 

https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies.html

When a principal tries to use the AWS Management Console, the AWS API, or the AWS CLI, that principal sends a request to AWS. When an AWS service receives the request, AWS completes several steps to determine whether to allow or deny the request.

https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_evaluation-logic.html#policy-eval-denyallow

You can create a customer managed policy in the AWS Management Console using one of the following methods:

- JSON — Paste and customize a published example identity-based policy.
- Visual editor — Construct a new policy from scratch in the visual editor. If you use the visual editor, you do not have to understand JSON syntax. 
- Import — Import and customize a managed policy from within your account. 

You can find more information on each of these methods in the IAM documentation. 

https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_create-console.html#access_policies_create-json-editor

The AWS policy generator is a free resource that exists outside of the AWS Management Console.

https://awspolicygen.s3.amazonaws.com/policygen.html

I’m including this in the notes for you to have additional practice looking at example policies. Also, many of the policies would provide great starting points. 

Instead of trying to write something from scratch, you could copy one of these and customize it to your needs. 

https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_examples.html

## Core Concepts for Roles

Roles, being designed for delegating access, provide a secure method to handle short-term credentials, are reusable, and help in adhering to least-privilege access control. 

Make sure you become as familiar and comfortable with roles as you can, as they’ll likely be the most common type of access management you encounter. 

https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_terms-and-concepts.html

The AWS Security Token Service (STS) is a web service that enables you to request temporary, limited-privilege credentials for AWS Identity and Access Management (IAM) users or for users that you authenticate (federated users).

For more detailed information about using this service, you can find out more at Temporary Credentials. 

https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp.html

You may also want more information about the Assume Role API call. To learn about the Assume Role API call, the AssumeRoleWithSAML, or the AssumeRoleWithWebIdentity API calls, you can use these links: 

https://docs.aws.amazon.com/STS/latest/APIReference/API_AssumeRole.html

https://docs.aws.amazon.com/STS/latest/APIReference/API_AssumeRoleWithSAML.html

https://docs.aws.amazon.com/STS/latest/APIReference/API_AssumeRoleWithWebIdentity.html

To create a role, you can use the AWS Management Console, the AWS CLI, the Tools for Windows PowerShell, or the IAM API.

If you use the AWS Management Console, a wizard guides you through the steps for creating a role. The wizard has slightly different steps depending on whether you're creating a role for an AWS service, for an AWS account, or for a federated user.

https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create.html

You can assume a role through the AWS Command Line Interface (CLI) or you can switch to a role using the AWS Management Console. For more information about switching roles through the AWS Management console, you can view the AWS documentation:

https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_use_switch-role-console.html

You should consider using IAM roles over long-term credentials whenever possible. IAM roles are especially important in the following scenarios: 

- Providing access to services like Amazon EC2 that need access to other AWS services.
- Providing access to users in other AWS accounts access to resources in your AWS account. 
- Providing access to identities that are stored externally through a corporate identity provider or a social media website. 

https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_common-scenarios.html

The IAM service supports only one type of resource-based policy called a role trust policy, which is attached to an IAM role. A role trust policy defines the principals (“who”) that can assume a role. For more information on role trust policies and other concepts related to roles, view the documentation: 

https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_terms-and-concepts.html

Access management through account separation is very common, but that will often involve providing access to users in other accounts. A great way of securely providing this access is with temporary credentials through the use of roles.

https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_common-scenarios_aws-accounts.html

## Utilizing Roles

AWS Lambda provides a very flexible and powerful compute resource that can handle all manner of tasks. Dive deeper into this tool and see how it can best fit in with your use cases. 

https://docs.aws.amazon.com/lambda/latest/dg/gettingstarted-concepts.html

To use AWS credentials with applications running on an EC2 instance, you should use an IAM role. To do this, you will need to assign a role and its associated permissions to an EC2 instance and make them available to its application. This is done through an instance profile that contains the role. These temporary credentials can then be used in the application to make API calls to AWS services and resources. You can find out more information about how to associate a role with an EC2 instance here. 

https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_use_switch-role-ec2.html

A service-linked role is a unique type of IAM role that is linked directly to an AWS service. Service-linked roles are predefined by the service and include all the permissions that the service requires to call other AWS services on your behalf. The linked service also defines how you create, modify, and delete a service-linked role. For more information on which services use service-linked roles, check out the AWS documentation.

https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_aws-services-that-work-with-iam.html

Identity federation is a system of trust between two parties for the purpose of authenticating users and conveying information needed to authorize their access to resources.

You can use two AWS services to federate your workforce into AWS accounts and business applications: AWS Single Sign-On (SSO) or AWS Identity and Access Management (IAM).

https://aws.amazon.com/identity/federation/

## Best Practices and Troubleshooting Tools

AWS Identity and Access Management (IAM) provides a number of security features to consider as you develop and implement your own security policies. To help better secure your resources, check out the best practices’ documentation for IAM. 

https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html

Check out the AWS IAM Access Analyzer to help identify resources that may be accidentally shared externally. This will help you lock down policies and mitigate risk. 

https://docs.aws.amazon.com/IAM/latest/UserGuide/what-is-access-analyzer.html

You may also want to access your credentials report. View the documentation for a guide on how to generate and retrieve this information. 

https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_examples_iam-credential-report.html

AWS Single Sign-On (SSO) makes it easy to centrally manage access to multiple AWS accounts and business applications and provide users with single sign-on access to all their assigned accounts and applications from one place. With AWS SSO, you can easily manage access and user permissions to all of your accounts in AWS Organizations centrally. AWS SSO configures and maintains all the necessary permissions for your accounts automatically, without requiring any additional setup in the individual accounts

https://aws.amazon.com/single-sign-on/

AWS CloudTrail is an AWS service that helps you enable governance, compliance, and operational and risk auditing of your AWS account. Actions taken by a user, role, or an AWS service are recorded as events in CloudTrail. Events include actions taken in the AWS Management Console, AWS Command Line Interface, and AWS SDKs and APIs.

https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-user-guide.html

The AWS Policy Simulator is a great tool for both building and troubleshooting policies in AWS Identity and Access Management. Use it to find issues in the policies and permissions you are trying to control, or just use it to test how policies and permissions work with various actions. 

It may also help to bookmark the policy simulator so that you can access it directly whenever you need. Just remember that you need to be signed into an account in order to use it. 

https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_testing-policies.html

https://policysim.aws.amazon.com/

To decode an authorization message, you can use an AWS STS API call. For more information on this API call and how to use it, view the AWS STS API Reference.

https://docs.aws.amazon.com/STS/latest/APIReference/API_DecodeAuthorizationMessage.html#API_DecodeAuthorizationMessage_Errors