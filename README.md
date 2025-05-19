# Self-Healing-Serverless-Security-in-AWSCloud

This implementation provides an automated self-healing mechanism for S3 buckets that 
detects and reverts unauthorized public access settings using AWS Lambda, EventBridge, 
and CloudWatch.

The system uses the following AWS components:

1. S3 bucket (secured initially) 
2. Lambda function to detect and revert unauthorized public access 
3. EventBridge rule to trigger the Lambda when S3 bucket policies change 
4. CloudWatch metrics and alarms to monitor system effectiveness 
5. Test scenarios to validate the solution 

---

# Architecture Overview

1. Architecture diagram

![image](https://github.com/user-attachments/assets/cfcf4655-d987-4fd6-9e70-99b582df86a4)

2. Flow diagram
   
![image](https://github.com/user-attachments/assets/3391f635-dc87-4394-bc2c-57e3e08b9ce2)

---

# Implementation steps

1. Create the S3 Bucket
   
    a. Sign in to the AWS Management Console and navigate to S3.
   
    b. Click "Create bucket", enter a unique name, select region.
   
    c. Enable "Block all public access" settings and create the bucket.
   
    d. Verify under Permissions that Block all public access is ON.
    
2. Create the IAM Role for Lambda
 
    a. In IAM, create a new role with trusted entity "Lambda"
   
    b. Attach "AmazonS3FullAccess" and "AWSLambdaBasicExecutionRole" policies.
   
    c. Name the role "S3BucketSelfHealRole" and create it.
    
3. Create the Lambda Function

    a. In Lambda, create function "Author from scratch", Python 3.9 runtime.
   
    b. Assign the existing "S3BucketSelfHealRole" role.
   
    c. Add the provided Python code to detect and revert public access settings.
   
    d. Set Timeout to 30 seconds and deploy the function.
   
4. Create the EventBridge Rule
   
    a. In EventBridge, create rule "S3BucketPublicAccessDetection" with custom event pattern for S3 policy changes.
    
    b. Set target to the "S3BucketSelfHeal" Lambda function.
    
    c. Review and create the rule.
   
5. Set up Additional Scheduled Check
 
    a. Create EventBridge rule "S3BucketPeriodicCheck" scheduled every 6 hours. 
        
    b. Configure same Lambda as target with constant JSON input.

6. Set up CloudWatch Alarms and Dashboard
   
    a. In CloudWatch, create dashboard "S3SecuritySelfHealDashboard" with widgets for RemediationActions, EvaluationsPerformed, RemediationResponseTime.
    
    b. Create alarm "S3BucketRemediationActionAlarm" on RemediationActions >= 1 
    (optional).
    
   
7. Create CloudTrail Trail

    a. In CloudTrail, create a new trail named "SelfHealTrail".
    
    b. Configure to log S3 Data Events and management events.
    
    c. Enable multi-region operation and specify a dedicated S3 bucket for logs.
 
8. Monitoring and Metrics

    Given the controlled, manual simulation environment, the most meaningful metric to track 
    is the remediation response time. Other metrics such as invocation count are less relevant 
    without a production workload.
    
      • Primary Metric: Remediation Response Time: Time between detection and fix (ms).

---

# Testing

1. Use the Python script "s3_remediation_test.py" to simulate public access toggles.
   
2. Launch the test: "python s3_remediation_test.py --bucket secure-self-healing-bucket -
function S3BucketSelfHeal --iterations 50".

3. Review the generated "s3_remediation_test.log" for detailed latency and response data.

---

# Results

Based on the simulation runs in `s3_remediation_test.log`, the following averages were 
observed: 

• Client-side round-trip time (RTT) over 50 runs: 597.88 ms 

• Lambda-reported response time over 50 runs: 426.26 ms 

**S3 Remediation Test: Client RTT vs Lambda Reported Time:**

![image](https://github.com/user-attachments/assets/eb193ff3-d7a9-479a-9c15-3751a9b94881)

**Difference Between Client RTT and Lambda Reported Time**

![image](https://github.com/user-attachments/assets/da6fae56-02be-40da-881f-7aae8238380d)

These results demonstrate consistent remediation performance, with the client-side 
overhead averaging under 600 ms and the Lambda function processing taking 
approximately 426 ms on average. 




