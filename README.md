# CF Nested Stack example

A Nested stack of Cloudformation templates that creates the following resources:

* VPC
  * 3 Subnets (2 Private, 1 Public and their Route Tables)
  * NAT Gateway
  * Internet Gateway
  * Security Group
* EC2 Spot Fleet Request with Autoscaling
  * 3 IAM Roles and their policies (Fleet,Autoscaling,Instance)
  * Application AutoScaling Target
    * Scaling Policies (Up/Down)
  * Instance Profile
* S3 Bucket(s)
* EC2 instance(s)

## Prerequisites

* Ensure all CloudFormation template files contained in this repository are uploaded to your desired S3 bucket.
* There is a sub-folder in this repository named `prerequisites`, it contains some CloudFormation template files that must be run prior to running the main (root) CloudFormation stack.
  * These CloudFormation template files define the creation of macros that extend the basic CloudFormation capabilities and these macros are required by our templates.
  * **NOTE:** These macros only need to be created once but ***FOR EACH REGION*** that you deploy these CloudFormation templates. These macros use created lambda functions which are region dependent.

> For more information on these macros, see the [README in the prerequisites sub-folder](prerequisites/README.md).

## Uploading files to S3

To upload all the necessary files to your S3 bucket, you may use the AWS console or the aws cli with the provided command structure.

```bash
aws s3 sync . "s3://${S3_BUCKET}" --profile "${AWS_PROFILE}" --include '*.json' --exclude '.git/*' --exclude '*.md' --exclude '.*' --exclude '*.bak' --exclude 'tmp/*'
```

> **NOTE:** If you update the code relating to the parameters (and perhaps others), you may need to hard reload your browser before creating a stack as the AWS console caches some information and will not render the changes from the code.

## Assumptions

* The same KeyPair is used to access all machines created by that resource stack.
* A single security group with SSH access attached to all the machines.
  * The CIDR range for the security group is defined by the `allowedCIDR` parameter (The default is from ***anywhere***).

## Variables

Before creating a CloudFormation stack, ensure you've uploaded the required files to an S3 bucket.

| Name                  | Description                                           |
|:----------------------|:------------------------------------------------------|
|templateBucketURL      |URL to an S3 Bucket, joined with template filename     |

The correct URL format can be attained by locating the `Object URL` when viewing one of the files in your S3 bucket.

> Make sure to exclude the path and file name after the `.com/` of the `Object URL`.

Insert the Object URL of the root.json file as templateBucketURL without the file-name.

> For example: `https://my-bucket.s3-us-west-2.amazonaws.com/`  
> **NOTE:** the url must end with a forward-slash `/`.

### VPC

| Name                  | Description                                           |
|:----------------------|:------------------------------------------------------|
|vpcCIDR                | CIDR Range for the VPC                                |
|allowedCIDR            | Allowed CIDR Range to be used in the security group   |

### Spot Fleet

| Name                       | Description                                                                                                                                |
|:---------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------|
|spotFleetInstanceType       | Spot Fleet Instance type. See [Accelerated Computing instance types](https://aws.amazon.com/ec2/instance-types/)                           |
|spotFleetAllocationStrategy | Indicates how to allocate the target Spot Instance capacity across the Spot Instance pools specified by the Spot Fleet request             |
|spotFleetKeyPairName        | EC2 KeyPair name for the Spot Fleet                                                                                                        |
|spotFleetMaximumCapacity    | The maximum number of units to request for the Spot Fleet                                                                                  |
|spotFleetTargetCapacity     | The number of units to request for the Spot Fleet                                                                                          |
|spotFleetPrice              | The maximum price per unit hour that you are willing to pay for a Spot Instance. The default is the On-Demand price                        |
|spotFleetRequestType        | One of [instant \| maintain \| request]. [Spot Fleet Requests](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/spot-fleet-requests.html) |
|spotFleetS3BucketName       | Allow Spot Fleet instances access to the specified S3 bucket                                                                               |
|spotFleetUserData           | Commands to execute after the instance starts                                                                                              |

## Debug

Here are a few tips when trying to monitor a deploying stack, or debugging a failed one.

### Monitoring

* There are 2 components to the stack view in the AWS console after deploying; the left bar containing the stack-list and on the right, the stack-details with the selected stack's details. Both have their own refresh button and they do not refresh automatically.
* You can monitor the `Events` tab of the stack-details component.

### Debugging

* Check for errors in the `Events` tab of the stack-details component.
* If a component fails without a detailed error, do the following:
  * Navigate to the `Resources` tab.
  * Follow the `Physical ID` link of the failed resource.
  * Check the `Events` tab of this stack and look for more detailed errors.
* Check to make sure `templateURL` parameter is pointing to the correct bucket uses the correct syntax.
* Make sure you approved each checkbox at the end of the last page of the stack creation.
