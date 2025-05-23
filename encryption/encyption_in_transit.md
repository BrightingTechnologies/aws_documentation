# Encryption in transit

- [Purpose](#purpose)
- [Scope](#scope)
- [Encryption in transit](#encryption-in-transit)
    - [Enforcing encryption in transit](#enforcing-encryption-in-transit)
      - [Security group](#1-security-group)
      - [VPN](#2-vpn)
      - [Resource policy](#3-resource-policy)
      - [AWS Certificate Manager](#4-aws-certificate-manager)
    - [Traffic encryption in endpoints exposed to the Internet](#traffic-encryption-in-endpoints-exposed-to-the-internet)
      - [API Gateway endpoints](#1-api-gateway-endpoints)
      - [Application Load Balancer endpoints](#2-application-load-balancer-endpoints)
      - [Lambda Function URLs](#3-lambda-function-urls)
      - [CloudFront](#4-cloudfront)
    - [Traffic encryption in processes that make requests to external endpoints over the Internet](#traffic-encryption-in-processes-that-make-requests-to-external-endpoints-over-the-internet)
      - [Lambda](#1-lambda)
      - [S3](#2-s3)

---

# Purpose

This document defines best practices and guidelines for setting up data encryption policy for data in transit.

# Scope

These best practices and guidelines apply to all projects under the management of [Company Name].

# Encryption in transit

Data in transit is any data that is sent from one system to another. This includes communication 
between resources within your workload as well as communication between other services and your end users. To [protect data in transit](https://docs.aws.amazon.com/wellarchitected/latest/security-pillar/protecting-data-in-transit.html) organizations we need to enforce encryption in transit and provide the appropriate level of protection for communication between resources.

## Enforcing encryption in transit

### 1. Security group

Configure a security group to only allow the HTTPS protocol.

How to create a security group:
- Open the Amazon VPC console
- In the navigation pane, select Security Groups
- Choose Create security group
- Provide a name and description for the security group
- Specify the VPC
- In the Inbound rules section, choose Add rule
- Set the Type to HTTPS (this automatically sets the Protocol to TCP and the Port Range to 443)
  - For Source specify the IP range that is allowed to access your resource
- Optional - if you want to restrict the outbound traffic, modify the outbound rules accordingly

How to assign the security group:

For an EC2 instance:

- Open the Amazon EC2 console
- Select Instances from the navigation pane 
- Choose the instance you want to associate with the security group
- Select Actions > Security > Change security groups
- Select the security group you created, and choose Add security group
- Select Save

For an ALB:

- Open the Amazon EC2 console
- Select Load Balancers from the navigation pane
- Choose your load balancer
- On the Security tab, choose Edit
- Select the security group you created

Choose Save changes.

### 2. VPN

AWS offers a managed VPN service that uses IPsec to secure your connections—ideal for point-to-point or network-to-network connectivity. With AWS Site-to-Site VPN, you can establish encrypted tunnels between your on-premises network (or another network) and your Amazon VPC.

Use the following procedure to [set up an AWS Site-to-Site VPN connection](https://docs.aws.amazon.com/vpn/latest/s2svpn/SetUpVPNConnections.html).

### 3. Resource policy

Use resource policies to enforce encryption in transit at resource level (e.g. Amazon S3 buckets).

In the following example, the bucket policy explicitly denies HTTP requests:
  ```json
{
  "Version": "2012-10-17",
  "Statement": [{
    "Sid": "RestrictToTLSRequestsOnly",
    "Action": "s3:*",
    "Effect": "Deny",
    "Resource": [
      "arn:aws:s3:::amzn-s3-demo-bucket",
      "arn:aws:s3:::amzn-s3-demo-bucket/*"
    ],
    "Condition": {
      "Bool": {
        "aws:SecureTransport": "false"
      }
    },
    "Principal": "*"
  }]
}
   ```

List of services that support resource-based policies can be found [here](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_aws-services-that-work-with-iam.html).

### 4. AWS Certificate Manager

By using ACM (AWS Certificate Manager), you obtain and manage SSL/TLS certificates that you then associate with your endpoints. This ensures that all traffic between your clients and your services (whether through ALB, API Gateway, or CloudFront) is encrypted in transit.

AWS Certificate Manager best practices can be found [here](https://docs.aws.amazon.com/acm/latest/userguide/acm-bestpractices.html).

## Traffic encryption in endpoints exposed to the Internet

### 1. API Gateway endpoints

All the APIs created with Amazon API Gateway expose HTTPS endpoints only. Amazon API Gateway does not support unencrypted (HTTP) endpoints.


### 2. Application Load Balancer endpoints

For Application Load Balancers (ALBs), encryption in transit is enforced by [configuring an HTTPS listener](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/create-https-listener.html) on your load balancer.

### 3. Lambda Function URLs

[Lambda Function URLs](https://docs.aws.amazon.com/lambda/latest/dg/urls-configuration.html) are HTTPS endpoints by default, ensuring encryption in transit.

### 4. CloudFront

Configure secure protocols in edge services. To enforce HTTPS:
- In the CloudFront console select your distribution and go to the:
    - Behaviors tab and edit the desired behavior and set Viewer Protocol Policy to HTTPS Only (connections are encrypted when CloudFront communicates with viewers)
    - Origins tab and edit the origin and set the Origin Protocol Policy to HTTPS Only (connections are encrypted when CloudFront communicates with your origin)

To serve secure content over SSL/TLS, CloudFront requires that SSL/TLS certificates be installed on either the CloudFront distribution or on the backed content source. [ACM is integrated with CloudFront](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/cnames-and-https-procedures.html#cnames-and-https-updating-cloudfront) to deploy ACM certificates on the CloudFront distribution.

Important:
- It is recommended that ACM is used to provision, manage, and deploy SSL/TLS certificates on AWS managed resources but beware that in an ACM certificate must be requested in the US East (N. Virginia) Region.


## Traffic encryption in processes that make requests to external endpoints over the Internet

### 1. Lambda

Outbound HTTPS Calls:
- Only use the HTTPS endpoints to make HTTP requests to external endpoints using tools like Axios.

Secure sensitive data in environment variables:
- [AWS Secrets Manager](https://docs.aws.amazon.com/secretsmanager/latest/userguide/retrieving-secrets_lambda.html)
  - Store sensitive data in Secrets Manager and retrieve it at runtime. This method offloads the decryption and key rotation responsibilities to a dedicated service.

- [Parameter Store](https://docs.aws.amazon.com/systems-manager/latest/userguide/ps-integration-lambda-extensions.html)
  - Use AWS Systems Manager Parameter Store (type of parameter SecureString) to securely store and retrieve sensitive configuration values.

- [Encryption in transit](https://docs.aws.amazon.com/lambda/latest/dg/configuration-envvars-encryption.html)
    - For additional security, you can enable helpers for encryption in transit, which ensures that your environment variables are encrypted client-side for protection in transit.

### 2. S3

[Client-side encryption](https://docs.aws.amazon.com/AmazonS3/latest/userguide/UsingClientSideEncryption.html) is the act of encrypting your data locally to help ensure its security in transit and at rest.

To encrypt your objects before you send them to Amazon S3, use the [Amazon S3 Encryption Client](https://docs.aws.amazon.com/amazon-s3-encryption-client/latest/developerguide/what-is-s3-encryption-client.html).

The Amazon S3 Encryption Client is supported in the following programming languages and platforms:
- Java
- Go
- C++ (AWS SDK for C++)
- .NET (v2) (AWS SDK for .NET)
- Ruby (v2) (AWS SDK for Ruby)
- PHP (v3) (AWS SDK for PHP)

In the following example, you can see both encryption and decryption for secure object storage:
  ```java
  
package software.amazon.encryption.s3.examples;

import software.amazon.awssdk.core.ResponseBytes;
import software.amazon.awssdk.core.async.AsyncRequestBody;
import software.amazon.awssdk.core.async.AsyncResponseTransformer;
import software.amazon.awssdk.services.s3.S3AsyncClient;
import software.amazon.awssdk.services.s3.model.GetObjectResponse;
import software.amazon.awssdk.services.s3.model.PutObjectResponse;
import software.amazon.encryption.s3.S3AsyncEncryptionClient;

import java.util.concurrent.CompletableFuture;

import static org.junit.jupiter.api.Assertions.assertEquals;
import static software.amazon.encryption.s3.utils.S3EncryptionClientTestResources.KMS_KEY_ID;
import static software.amazon.encryption.s3.utils.S3EncryptionClientTestResources.appendTestSuffix;

public class AsyncClientExample {
public static final String OBJECT_KEY = appendTestSuffix("async-client-example");

    public static void main(final String[] args) {
        String bucket = args[0];
        AsyncClient(bucket);
        cleanup(bucket);
    }

    /**
     * This example demonstrates handling of an Asynchronous S3 Encryption Client for interacting with an Amazon S3 bucket, performing
     * both encryption and decryption using the AWS KMS Key Arn for secure object storage.
     *
     * @param bucket The name of the Amazon S3 bucket to perform operations on.
     */
    public static void AsyncClient(String bucket) {
        final String input = "PutAsyncGetAsync";

        // Instantiate the S3 Async Encryption Client to encrypt and decrypt
        // by specifying a KMS key with the kmsKeyId parameter.
        //
        // This means that the S3 Async Encryption Client can perform both encrypt and decrypt operations
        // as part of the S3 putObject and getObject operations.
        S3AsyncClient v3AsyncClient = S3AsyncEncryptionClient.builder()
                .kmsKeyId(KMS_KEY_ID)
                .build();

        // Call putObject to encrypt the object and upload it to S3
        CompletableFuture<PutObjectResponse> futurePut = v3AsyncClient.putObject(builder -> builder
                .bucket(bucket)
                .key(OBJECT_KEY)
                .build(), AsyncRequestBody.fromString(input));
        // Block on completion of the futurePut
        futurePut.join();

        // Call getObject to retrieve and decrypt the object from S3
        CompletableFuture<ResponseBytes<GetObjectResponse>> futureGet = v3AsyncClient.getObject(builder -> builder
                .bucket(bucket)
                .key(OBJECT_KEY)
                .build(), AsyncResponseTransformer.toBytes());
        // Just wait for the future to complete
        ResponseBytes<GetObjectResponse> getResponse = futureGet.join();

        // Assert
        assertEquals(input, getResponse.asUtf8String());

        // Close the client
        v3AsyncClient.close();
    }

    private static void cleanup(String bucket) {
        // Instantiate the client to delete object
        S3AsyncClient v3Client = S3AsyncEncryptionClient.builder()
                .kmsKeyId(KMS_KEY_ID)
                .build();

        // Call deleteObject to delete the object from given S3 Bucket
        v3Client.deleteObject(builder -> builder.bucket(bucket)
                .key(OBJECT_KEY)).join();

        // Close the client
        v3Client.close();
    }
}
  ```
