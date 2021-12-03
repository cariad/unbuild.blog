---
date: 2021-12-03
title: Why do you care what your S3 buckets are named?
tags:
  - amazon web services
---

Some folks really, _really_ care about the names of their S3 buckets.

They'll spend hours to days thinking up their patterns and schemas, considering edge cases and future requirements, then commit and find themselves spending just as much energy trying to enforce the decision until it's inevitably retired in favour of a _new and improved_ convention.

Folks. The names of your S3 buckets aren't important. At least, they don't need to be.

Perhaps they _shouldn't_ be.

<!--more-->

I think the main reason why folks give their buckets specific names is to help with IAM policies. Say, for example, you're creating an IAM role that needs to write to your European platform's log. You might write a policy like this:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Action": "s3:PutObject",
      "Effect": "Allow",
      "Resource": "arn:aws:s3:::sugarwater-prodeu-logs/*"
    }
  ]
}
```

For sure, that works. So what's the problem?

## Buckets and assholes are everywhere

The problem is twofold:

1. S3 bucket names are globally unique.
1. Patterns can be abused.

Let's take an example. Say your infrastructure-as-code at Sugarwater Inc. deploys buckets with the convention `sugarwater-<environment>-<usage>`.

This gives you buckets like:

- `sugarwater-demo-imports`
- `sugarwater-uat-recordings`
- `sugarwater-produs-exports`

There are patterns here, and they can be abused at essentially zero cost by anyone in the world who cares.

The first pattern is the environment name. Maybe right now you deploy only a US platform, but you finally -- finally! -- got a customer in Oslo and it's time to deploy an EU-resident platform. So you run your infrastructure-as-code scripts with `environment=prodeu` and... it fails.

Someone -- anyone -- has already created a bucket named `sugarwater-prodeu-imports`. You'll never know who. You can't stop them. But they had faith that you'd get that European customer, took a guess what you'd call your new platform, then took out a chunk of your day with a deployment failure.

You could always try again with `prodeuro` instead. Maybe that'll work. But it's not the end.

The second pattern is the usage. Maybe one day you decide to start storing each environment's logs in S3, so you update your infrastructure-as-code to deploy `sugarwater-<environment>-logs` and... boom.

Someone -- anyone -- has already created a bucket named `sugarwater-produs-logs`. They spotted your pattern, figured logging is an obvious use for an S3 bucket, and took a wild guess that you'd call it "logs". You could try again with, what, `applogs`? Sure. Why not.

## It doesn't take luck

You might be thinking that this kind of mischief leans on a person having a _lot_ of luck without any real payoff.

Oh, there's a payoff.

Here's our original IAM policy again:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Action": "s3:PutObject",
      "Effect": "Allow",
      "Resource": "arn:aws:s3:::sugarwater-prodeu-logs/*"
    }
  ]
}
```

Say your European services get deployed with that policy attached. What's the worst that could happen?

Sure, the bucket might not exist.

But what if it _does_ exist, but you don't own it?

What if the cheeky squatter counted on you being stressed and rushed by your deployments breaking, and applied a policy to their bucket that grants your account permission to upload anything it likes?

You trusted in your naming convention, and now you're shipping your logs to a competitor, or worse.

And don't you believe for a second that it takes an iota of luck to pull off a bucket name conflict. You don't need luck when you can pull and read the infrastructure-as-code scripts for yourself.

Office politics, bigotry and redundancies nudge folks into making strange, career-ending decisions. The call is more likely to come from inside the house than across any ocean.

So, let's stop making up our own names for our buckets. Let AWS find any available name for you and be happy with it.

What do our IAM policies look like then?

## Managed policies

One option you might consider is _tagging_. If a bucket's policy requires uploaded objects to have a specific tag, and your IAM roles have policies that either allow or deny their ability to use that tag, then you can limit each role's ability to use each bucket.

For smaller and simpler deployments, though, _managed policies_ might be enough.

The following CloudFormation template deploys:

- A bucket with any available name.
- A Systems Manager parameter to hold the bucket's generated name.
- A managed policy that grants permission to read the Systems Manager parameter to get the bucket's name, and read objects from that bucket.
- A managed policy that grants permission to read the Systems Manager parameter to get the bucket's name, and put objects into that bucket.

```yaml
Description: S3 bucket demo

Parameters:
  LogicalName:
    Type: String

Resources:
  Bucket:
    Type: AWS::S3::Bucket
    Properties:
      PublicAccessBlockConfiguration:
        BlockPublicAcls: true
        BlockPublicPolicy: true
        IgnorePublicAcls: true
        RestrictPublicBuckets: true

  Parameter:
    Type: AWS::SSM::Parameter
    Properties:
      Name:
        Fn::Sub: /Buckets/${LogicalName}
      Type: String
      Value:
        Ref: Bucket

  AllowRead:
    Type: AWS::IAM::ManagedPolicy
    Properties:
      Description:
        Fn::Sub: Provides read-only access to the ${LogicalName} bucket
      ManagedPolicyName:
        Fn::Sub: ${LogicalName}ReadOnly
      PolicyDocument:
        Version: 2012-10-17
        Statement:
          - Action:
            - s3:ListBucket
            Effect: Allow
            Resource:
              Fn::Sub: arn:aws:s3:::${Bucket}

          - Action:
              - s3:GetObject
            Effect: Allow
            Resource:
              Fn::Sub: arn:aws:s3:::${Bucket}/*

          - Action:
              - ssm:GetParameter
            Effect: Allow
            Resource:
              Fn::Sub: arn:aws:ssm:${AWS::Region}:${AWS::AccountId}:parameter/Buckets/${LogicalName}

  AllowWrite:
    Type: AWS::IAM::ManagedPolicy
    Properties:
      Description:
        Fn::Sub: Provides write-only access to the ${LogicalName} bucket
      ManagedPolicyName:
        Fn::Sub: ${LogicalName}WriteOnly
      PolicyDocument:
        Version: 2012-10-17
        Statement:
          - Action:
              - s3:PutObject
            Effect: Allow
            Resource:
              Fn::Sub: arn:aws:s3:::${Bucket}/*

          - Action:
              - ssm:GetParameter
            Effect: Allow
            Resource:
              Fn::Sub: arn:aws:ssm:${AWS::Region}:${AWS::AccountId}:parameter/Buckets/${LogicalName}
```

Say you deploy that template with the local name set to "Builds". This will create:

- A bucket with an unknown name.
- A Systems Manager parameter named `/Buckets/Build` that holds the bucket's name.
- Managed policies named `BuildsReadOnly` and `BuildsWriteOnly` that grant access to the parameter and the bucket.

With that stack deployed, I can assign the `BuildsWriteOnly` policy to myself then discover the bucket's name with:

```bash
aws ssm get-parameter --name /Buckets/Builds
```

<!--dinject as=markdown fence=backticks host=shell range=start-->

```text
{
  "Parameter": {
    "Name": "/buckets/Builds",
    "Type": "String",
    "Value": "demo-bucket-1x4ujw160r3fd",
    "Version": 1,
    "LastModifiedDate": "2021-12-03T11:16:57.432000+00:00",
    "ARN": "arn:aws:ssm:eu-west-2:807041577214:parameter/buckets/Builds",
    "DataType": "text"
  }
}
```

<!--dinject range=end-->

Now I can upload and -- if I assign `BuildsReadOnly` too -- download objects from the bucket.

With a few tweaks, your managed policies can be automatically assigned to the correct roles at deploy-time. You don't have to -- also, shouldn't -- assign them manually. But with that one policy comes all the permissions that your role needs. The only change to any code that needs to know the bucket name would be to have it look-up the name in Systems Manager rather than be configured or guess.

## Is it worth it?

With infrastructure-as-code like the example above, you don't need a naming convention for your buckets and everything still works out fine.

There are no meetings to decide how buckets should named. There are no assumptions about what the names will be. There are no patterns that can be abused outside of your Amazon Web Services account.

You don't need to name your buckets.
