# Voicemail for Amazon Connect
This solutions deploys the resources necessary to configure a voicemail system to use with Amazon Connect. See [Solution Architecture](https://aws.amazon.com/solutions/implementations/voicemail-for-amazon-connect/).

## Running unit tests for customization
* Clone the repository, then make the desired code changes
* Install jest
```
npm install -g jest
```
* Next, run unit tests to make sure added customization passes the tests
```
cd ./source/aws-connect-vm-serverless
npm run test
```

## Building distributable for customization
* Configure the bucket name of your target Amazon S3 distribution bucket
```
export DIST_OUTPUT_BUCKET=vf-vmail-<REGION> # bucket where customized code resides in the vf-saas-infra account
export SOLUTION_NAME=vf-vmail
export VERSION=1.3.0 # version number for the customized code
```
_Note:_ We have created am S3 bucket with the name 'vf-vmail-ap-southeast-2'; The assets in bucket are accessible from the accounts you will run the CloudFormation stack currently as this is public but will change to be whitelisted accounts only.

* The build script requires Maven and npm; please ensure you have both of these installed in your environment.
* Now build the distributable:
```
cd ./deployment
chmod +x ./build-s3-dist.sh
./build-s3-dist.sh $DIST_OUTPUT_BUCKET $SOLUTION_NAME $VERSION
```

* Deploy the distributable to an Amazon S3 bucket in your account. _Note:_ you must have the AWS Command Line Interface installed.
```
aws s3 cp ./regional-s3-assets  s3://$DIST_OUTPUT_BUCKET/$SOLUTION_NAME/$VERSION/ --recursive
aws s3 cp ./global-s3-assets  s3://$DIST_OUTPUT_BUCKET/$SOLUTION_NAME/$VERSION/ --recursive
```

* Get the link of the solution template uploaded to your Amazon S3 bucket.
* Deploy the solution to your account by launching a new AWS CloudFormation stack using the link of the solution template in Amazon S3.

*** 

## File Structure

```
|-deployment/
  |-build-s3-dist.sh                        [ shell script for packaging distribution assets ]
  |-run-unit-tests.sh                       [ shell script for executing unit tests ]
  |-voicemail-for-amazon-connect.template   [ solution CloudFormation deployment template ]
|-source/
  |-aws-connect-vm-serverless               [ Backend functions for processing voicemails ]
  |-aws-connect-vm-portal                   [ Voicemail configuration portal ]

```

Each microservice follows the structure of:

```
|-service-name/
  |-lib/
    |-[service module libraries and unit tests]
  |-index.js [injection point for microservice]
  |-package.json
```

***

This solution collects anonymous operational metrics to help AWS improve the
quality of features of the solution. For more information, including how to disable
this capability, please see the [implementation guide](voicemail-for-amazon-connect-implementation-guide.pdf).

Copyright 2020 Amazon.com, Inc. or its affiliates. All Rights Reserved.

Licensed under the Apache License Version 2.0 (the "License"). You may not use this file except in compliance with the License. A copy of the License is located at

    http://www.apache.org/licenses/

or in the "license" file accompanying this file. This file is distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, express or implied. See the License for the specific language governing permissions and limitations under the License.
