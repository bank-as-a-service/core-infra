# AWS Load Balancer Controller Policy
You only need to create this policy once per account.
Just update the trust policy with the correct values for your account.

1. create the policy
```shell
aws iam create-policy \
  --policy-name AWSLoadBalancerControllerIAMPolicy \
  --policy-document file://iam_policy.json
```

2. create the trust policy (TODO: automate this)
```
- determine the OpenID Connect provider URL from the AWS EKS Dashboard
- determine the AWS Account ID
- REPLACE the values in the trust policy below
```
```shell
cat >load-balancer-role-trust-policy.json <<EOF
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": {
                "Federated": "arn:aws:iam::<AWS_ACCOUNT_ID>:oidc-provider/oidc.eks.region-code.amazonaws.com/id/<OPEN_ID_CONNECT_ID>"
            },
            "Action": "sts:AssumeRoleWithWebIdentity",
            "Condition": {
                "StringEquals": {
                    "oidc.eks.region-code.amazonaws.com/id/<OPEN_ID_CONNECT_ID>:aud": "sts.amazonaws.com",
                    "oidc.eks.region-code.amazonaws.com/id/<OPEN_ID_CONNECT_ID>:sub": "system:serviceaccount:kube-system:aws-load-balancer-controller"
                }
            }
        }
    ]
}
```

3. create the IAM role
```shell
aws iam create-role \
  --role-name AmazonEKSLoadBalancerControllerRole \
  --assume-role-policy-document file://load-balancer-role-trust-policy.json
```

4. attach the policy to the role
```shell
aws iam attach-role-policy \
  --policy-arn arn:aws:iam::<AWS_ACCOUNT_ID>:policy/AWSLoadBalancerControllerIAMPolicy \
  --role-name AmazonEKSLoadBalancerControllerRole
```