# CP4D Steps to EXPOSE IADB on AWS cluster

Follow the steps from here
https://sanjitcibm.medium.com/externalize-database-from-information-server-ae57b0faca77

# Commands to Expose the Port on the AWS service using AWS CLI

## Create new target group for the DB2 worker

aws elbv2 create-target-group \
    --name cpd-worker-30586 \
    --protocol TCP \
    --port 30586 \
    --target-type instance \
    --vpc-id vpc-0522cca6a844cd4f0

## Register the target group for DB2 workerto target ARN group

aws elbv2 register-targets \
    --target-group-arn arn:aws:elasticloadbalancing:us-east-1:555157090578:targetgroup/cpd-worker-30586/ec5c0b29071f4f05 \
    --targets Id=i-0b716e47e4fd28051 Id=i-084784ff95b385f4c Id=i-0e0795cee50ee9cc4 Id=i-0193bdc71cddaf6c9 Id=i-0249e671e0ff89fbd Id=i-0be4f2cb31399a4aa
 
## Create the target listener for DB2 workerto target ARN group

aws elbv2 create-listener --load-balancer-arn arn:aws:elasticloadbalancing:us-east-1:555157090578:loadbalancer/net/cpd-dev-dwr2t-ext/6f046bdc0553e227 \
                          --protocol TCP --port 30586 \
                          --default-actions Type=forward,TargetGroupArn=arn:aws:elasticloadbalancing:us-east-1:555157090578:targetgroup/cpd-worker-30586/ec5c0b29071f4f05

## Describe the target group

aws elbv2 describe-target-groups --target-group-arns arn:aws:elasticloadbalancing:us-east-1:555157090578:targetgroup/cpd-worker-30586/ec5c0b29071f4f05

## Add the port to ingress

aws ec2 authorize-security-group-ingress --group-id sg-03b73aebab7d59812 --protocol tcp --port 30586 --cidr 10.0.0.0/8 

## Check the port to ingress

aws ec2 describe-security-groups --group-ids sg-03b73aebab7d59812
