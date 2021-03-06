# cogito-resource

[![Build Status](https://github.com/cogito-lang/cogito-resource/workflows/Main/badge.svg)](https://github.com/cogito-lang/cogito-resource/actions)

A CloudFormation [custom resource](http://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/template-custom-resources.html) lambda handler that enables you to write [libcogito](https://github.com/cogito-lang/libcogito) syntax in your CloudFormation templates using the python [cogito](https://pypi.python.org/pypi/cogito) package.

## Dependencies

Ensure you have [`serverless`](https://serverless.com/) and [`pip`](https://pypi.python.org/pypi/pip) installed.

## Getting started

Download the latest `libcogito` amazon linux package from the GitHub release page:

    curl https://github.com/cogito-lang/libcogito/releases/download/v0.2.0/libcogito.so -o libcogito.so

Install the python dependencies into the `vendor` directory:

    pip install -t vendor -r requirements.txt

Run `serverless` to deploy:

    serverless deploy

Take that outputted arn for the lambda and use that to build a custom resource in CloudFormation like:

```json
{
  "Resources": {
    "CogitoResource": {
      "Type": "Custom::CogitoResource",
      "Version": "1.0",
      "Properties": {
        "ServiceToken": "arn:aws:lambda:us-east-1:000123456789:function:cogito-dev-cogito",
        "Policy": "ALLOW s3:GetObject ON *;"
      }
    },
    "CogitoPolicy": {
      "Type": "AWS::IAM::ManagedPolicy",
      "Properties": {
        "PolicyDocument": { "Fn::GetAtt": ["CogitoResource", "PolicyDocument"] }
      }
    }
  }
}
```

Deploy your CloudFormation stack and you will now have an IAM ManagedPolicy resource with the expanded IAM permission syntax below:

```json
{
  "Effect": "Allow",
  "Action": [
    "s3:GetObject"
  ],
  "Resource": "*"
}
```

For a more verbose example, see the [example](example) directory.

## Development

To test the lambda handler, run `python test/server.py`. This will fork into two processes, one running a server hosted to receive the callback, and one sending a request to the handler. It then asserts against the returned JSON response.

## Contributing

Bug reports and pull requests are welcome on GitHub at https://github.com/cogito-lang/cogito-resource.

## License

The code is available as open source under the terms of the [MIT License](https://opensource.org/licenses/MIT).
