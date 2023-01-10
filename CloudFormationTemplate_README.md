# Create a CloudFormation template using YAML 
#### Templates are created in YAML using references from https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-template-resource-type-ref.html

## Story :
### In this project, we will be utilizing CloudFormation template and YAML script to create S3 and EC2 instances. Will see how by creating a logical resource created via CloudFormation stack will create similar physical resource/s in your account 

## Steps : 
1. Create a YAML script using Visual Studio or any other IDE -- this script will have details of creating S3 bucket and an EC2 instance

![image](https://user-images.githubusercontent.com/73116712/211220344-f6bb752c-77f7-478f-b0ae-b0d11b4cd81f.png)

2. Goto CloudFormation , Create Stack
3. Select "Template is ready"
4. Under Template Source, select "Upload a template file" and select the YAML template we created in the above step. 
5. Select "Next"
6. Stack Name -- "MyFirstStack". Select Next
7. Create Stack . For all the logical resource we provided in the YAML file, its corresponding physical resource will be created 
8. in couple of mins, both the resources should be created and Status shouls change to *Create_Complete*
9. Check under Resources tab to confirm the Resources are created 

## Cleanup :
1. Delete the stack which was created - this will also cleanup all the resources created via this stack 
