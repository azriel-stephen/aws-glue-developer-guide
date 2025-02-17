# Known Issues for AWS Glue<a name="glue-known-issues"></a>

Note the following known issues for AWS Glue\.

**Topics**
+ [Preventing Cross\-Job Data Access](#cross-job-access)

## Preventing Cross\-Job Data Access<a name="cross-job-access"></a>

Consider the situation where you have two AWS Glue Spark jobs in a single AWS Account, each running in a separate AWS Glue Spark cluster\. The jobs are using AWS Glue connections to access resources in the same virtual private cloud \(VPC\)\. In this situation, a job running in one cluster might be able to access the data from the job running in the other cluster\. 

The following diagram illustrates an example of this situation\.

![\[AWS Glue job Job-1 in Cluster-1 and Job-2 in Cluster-2 are communicating with an Amazon Redshift instance in Subnet-1 within a VPC. Data is being transferred from Amazon S3 Bucket-1 and Bucket-2 to Amazon Redshift.\]](http://docs.aws.amazon.com/glue/latest/dg/images/escalation-of-privs.png)

In the diagram, AWS Glue `Job-1` is running in `Cluster-1`, and Job\-2 is running in `Cluster-2`\. Both jobs are working with the same instance of Amazon Redshift, which resides in `Subnet-1` of a VPC\. `Subnet-1` could be a public or private subnet\. 

`Job-1` is transforming data from Amazon Simple Storage Service \(Amazon S3\) `Bucket-1` and writing the data to Amazon Redshift\. `Job-2` is doing the same with data in `Bucket-2`\. `Job-1` uses the AWS Identity and Access Management \(IAM\) role `Role-1` \(not shown\), which gives access to `Bucket-1`\. `Job-2` uses `Role-2` \(not shown\), which gives access to `Bucket-2`\.

These jobs have network paths that enable them to communicate with each other's clusters and thus access each other's data\. For example, `Job-2` could access data in `Bucket-1`\. In the diagram, this is shown as the path in red\.

To prevent this situation, we recommend that you attach different security configurations to `Job-1` and `Job-2`\. By attaching the security configurations, cross\-job access to data is blocked by virtue of certificates that AWS Glue creates\. The security configurations can be *dummy* configurations\. That is, you can create the security configurations without enabling encryption of Amazon S3 data, Amazon CloudWatch data, or job bookmarks\. All three encryption options can be disabled\.

For information about security configurations, see [Encrypting Data Written by Crawlers, Jobs, and Development Endpoints](encryption-security-configuration.md)\.

**To attach a security configuration to a job**

1. Open the AWS Glue console at [https://console\.aws\.amazon\.com/glue/](https://console.aws.amazon.com/glue/)\.

1. On the **Configure the job properties** page for the job, expand the **Security configuration, script libraries, and job parameters** section\.

1. Select a security configuration in the list\.