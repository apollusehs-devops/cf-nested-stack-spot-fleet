# Macros

Creates a Cloudformation stack for each macro. each stack creates a lambda function the macro uses.

## Count

* Original source code can be found [here](https://github.com/awslabs/aws-cloudformation-templates/tree/master/aws/services/CloudFormation/MacrosExamples/Count).

The `Count` macro provides a template-wide `Count` property for CloudFormation resources.

To make use of the macro, add `Transform: Count` to the top level of your CloudFormation template.

```json
{
  "AWSTemplateFormatVersion": "2010-09-09",
  "Description": "Infrastructure S3 CFN Template",
  "Transform": "Count",
  "Parameters": {
      ...
  },
  "Resources": {
      ...
  }
}
```

To create multiple copies of a resource, add a `Count` property with an integer value.

```json
      "Properties": {
      "Count": {
        "Ref": "s3BucketCount"
      }
    }
  }
```

## String

* Original source code can be found [here](https://github.com/awslabs/aws-cloudformation-templates/tree/master/aws/services/CloudFormation/MacrosExamples/StringFunctions).

The `String` macro provides a template-wide `String` property for CloudFormation resources.

To make use of the macro, Place the transform where you would like the output to be placed and provide the input string as the value for the `InputString` Parameter.

```json
{
    "Fn::Transform": {
        "Name": "String", "Parameters": {
            "InputString": { "Ref": "mystring" },
            "Operation": "Lower"
        }
    }
}
```
