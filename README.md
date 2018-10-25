# json_to_csv_conversion_repo
Git repo including python script along with input/output .json and .csv files

# Objective 1: 
To build a reusable JSON to CSV conversion module using python scripts locally with temp/source/destination directories configurable in the python script

						Objective 1 Description
							
This project converts nested JSON files generated in local temp directory, by uploading the last modified file from the temp to the source directory and then to convert these files into CSV format and upload them in a destination directory.

Nested input JSON file structure:
Please find below the nested JSON file containing the scnearios namely dictionary of dictionaries, list of dictionaries etc. with a good depth of nested values to be selected as the input JSON files.

JSON Files:

snacks_menu.json:
[   {   
        "id": "0001",
        "type": "donut",
        "name": "snack",
        "ppu": { "num" : "0.55", "num1" : "0.63" },
        "batters":
                {
                        "batter":
                                [
                                        { "id": "1001", "type": "Regular" },
                                        { "id": "1002", "type": "Chocolate" },
                                        { "id": "1003", "type": "Blueberry" },
                                        { "id": "1004", "type": "Devil's Food" }
                                ]
                },
        "topping":
                [
                        { "id": "5001", "type": "None" },
                        { "id": "5002", "type": "Glazed" },
                        { "id": "5005", "type": "Sugar" },
                        { "id": "5007", "type": "Powdered Sugar" },
                        { "id": "5006", "type": "Chocolate with Sprinkles" },
                        { "id": "5003", "type": "Chocolate" },
                        { "id": "5004", "type": "Maple" }
                ]
},

   {
        "id": "0002",
        "type": "burger",
        "name": "cheese",
        "ppu": { "num" : "0.50", "num1" : "0.61" },
        "batters":
                {
                        "batter":
                                [
                                        { "id": "1005", "type": "Regular" },
                                        { "id": "1006", "type": "Chocolate" },
                                        { "id": "1007", "type": "Blueberry" },
                                        { "id": "1008", "type": "Devil's Food" }
                                ]
                },
        "topping":
                [
                        { "id": "5008", "type": "None" },
                        { "id": "5014", "type": "Glazed" },
                        { "id": "5009", "type": "Sugar" },
                        { "id": "5010", "type": "Powdered Sugar" },
                        { "id": "5011", "type": "Chocolate with Sprinkles" },
                        { "id": "5012", "type": "Chocolate" },
                        { "id": "5013", "type": "Maple" }
                ]
   }
]


# Implementation steps:

Step 1: To prepare the input Nested JSON Files

Step 2: If local source directory already exists, then to upload the latest JSON file added in the temp (json file generator) directory or else to create the source directory and then to upload all the JSON files from temp to source directory.

Step 3: Conversion logic from JSON to csv either for the most recent file or for all the files for the first execution based on the result of step 2.

Step 4: To save .csv files in the destination directory path (to create if not exists).                                                 

# Directory path location:
```
temp dir: /home/ec2-user/tmp/
```

```
Source dir: /home/ec2-user/src    -  contains JSON files copied from the /tmp directory

Destination dir: /home/ec2-user/dest -  contains the converted CSV files corresponding to each JSON file in source
```

# Python scripts wrapping the conversion module:

```
Path: /json_to_csv_conversion_repo/json_to_csv_conversion_repo/

Main script: json_to_csv_conversion_process.py

Description: To process the latest JSON file uploded in a source location, by converting it into .csv format and then loading it into a target directory locally (File locations to be configurable at script).
Main script which configures temp, source and dest directories and calls the function convert_json_to_csv from the python script json_conversion.py by importing it as a custom module.

```
		Performance aspects considered while implementing this main script:

--> The directories are configurable from script i.e. the script has pre-validations whether the source and destination directories exists or not and to create them dynamically as well as copy the most recent file at run time dynamically.

--> The JSON to CSV conversion logic is wrapped inside a reusable function and it is called wherever required in the script using the below line of code where json_conversion is the python script containing this reusable function which is imported in the main script.

json_conversion.convert_json_to_csv(file_path)

This aspect improves the performance of dynamically calling a function object by importing the script as a seperate module.

```
JSON to CSV conversion logic: json_conversion.py

Description: Script containing function convert_json_to_csv which processes JSON files to .csv one at a time by calling from the main script

```
		Performance aspects considered while implementing this reusable JSON to CSV conversion function:

--> As a first step, using json.load module, converted the input nested JSON files into dictionaries by opening filepath passed as argument from main script.

--> Using pandas module to convert the JSON file structure into a dataframe object containing rows and columns similar to the format of a CSV file.

--> Used json_normalize method to flatten the JSON nested dictionaries in an efficient performance intensive way so that the JSON can be straightaway converted to a dataframe rows and columns structure without much hassle.

--> used json_normalize is used in an iterative way by identifying all the columns which has nested lists as values to flatten them further into multiple rows. This approach can be easily extended to convert any complex nested JSON files with a greater degree of depth into a CSV. 

--> The further flattened columns with nested lists as values are merged to the actual dataframe based on the row_index values which results in faster execution time for indexing operation instead of doing merge based on key columns.

--> The emphasis is on reducing the redundant lines of code and to improve readability. At the same time, reducing the number of iterations on the datasets so that the code achieves improved performance.

# How to Execute:

Step 1: Clone the repository using - 
```
  git clone git@github.com:tapaswivijay/json_to_csv_conversion_repo.git
```
Step 2: cd json_to_csv_conversion_repo/json_to_csv_conversion_repo/

Step 3: Execute the Main script using -
```
		python json_to_csv_conversion_process.py		
``` 
Step 4: The output converted CSV files are loaded in Destination dir for each corresponding Source dir JSON files.

# Objective 2: 
To implement this solution on the AWS Cloud platform by using serverless compute service such as Lambda along with the S3 object storage service.
				                      Objective 2 Description
						      
To automate the JSON to CSV conversion process using Lambda serverless compute service and to dynamically convert the latest JSON files generated in the temp bucket by configuring an S3 temp bucket event trigger which automatically initiates execution of Lambda function as and when any new file is generated. This project also contains the temp, source and destination S3 buckets configured within the python script to create the buckets or to cpoy the files dynamically inside the python code.

# AWS services used:
Lambda, S3, IAM Role to execute Lambda (having S3FullAccess policy to create buckets and cloudwatch Access policy for logging), ec2 instance to prepare the deployment package.

# AWS S3 buckets:

```
Temp bucket name: s3://json-temp-file-generator
Source bucket name: s3:// json-to-csv-source - created dynamically from Lambda function python code
Destination bucket name: S3:// json-to-csv-destination - created dynamically from Lambda python code
```

# Lambda function Python scripts:

```
Path: /json_to_csv_conversion_repo/json_to_csv_conversion_repo/lambda/

Handler function wrapper script: json_to_csv_conversion_process.py
Description: Python script containing AWS Lambda handler function to process the json files in source S3 bucket and converts them to .csv in the destination S3 bucket by calling the file conversion script json_conversion and reusable function.

```

			Implementation steps of the AWS Lambda python handler script

--> Prepared the deployment package by installing the external modules such as boto3, pandas etc. in an ec2 instance (Linux centos operating system) and then creating a root directory named "lambda" containing all these external module executable scripts and then creating directory "lib" (containing the shared object library files such as libblas.so.3, liblapack.so.3 etc.)

--> Using the urllib module to dynamically extract the JSON file name which triggered the lambda function using:

key = urllib.unquote_plus(event['Records'][0]['s3']['object']['key'])

This enables the lambda function to execute concurrently as parallel streams. For example, if 10 JSON file are uploaed in temp S3 bucket, the each JSON file will trigger a seperate lambda function resulting in 10 lambda functions running concurrently.

--> Using the boto3 module to connect to the AWS services such as S3, lambda etc. and then to dynamically create buckets, copy S3 objects, to upload converted files in bucket and to perform validations if S3 bucket already exists, to filter out files in s3 buckets.

--> Used error/exception handling in the python script to give customized user messages for the exceptions raised if any, thereby developing a good pythonic way of writing the code.

--> Included the cloud formation template using which the Lambda function creation can be automated (by modifying the properties as required) by creating stacks.

JSON to CSV conversion logic: json_conversion.py

Description: Script containing function convert_json_to_csv which processes JSON files to .csv one at a time by calling from the main script.

# AWS Execution Steps:
Step 1: To create the Temp S3 bucket (where JSON files are generated dynamically) and upload sample JSON file.

![alt text](https://github.com/tapaswivijay/json_to_csv_conversion_repo/blob/master/json_to_csv_conversion_repo/lambda/jsontocsv/temp_s3_bucket.PNG)
 
Step 2: To create an IAM Role having S3FullAccess policy to create buckets and cloudwatch Access policy for logging.

Step 3: To create a Lambda function using the below yaml file:

File location: 
```
https://github.com/tapaswivijay/json_to_csv_conversion_repo/blob/master/json_to_csv_conversion_repo/lambda/json-to-csv-converter.yaml
```

AWSTemplateFormatVersion: '2010-09-09'
Transform: 'AWS::Serverless-2016-10-31'
Description: An AWS Serverless Specification template describing your function.
Resources:
  jsontocsvconverter:
    Type: 'AWS::Serverless::Function'
    Properties:
      Handler: json_to_csv_conversion_process.handler
      Runtime: python2.7
      CodeUri: .
      Description: ''
      MemorySize: 1024
      Timeout: 300
      Role: 'arn:aws:iam::691208029096:role/json-to-csv-converter'
      Events:
        BucketEvent1:
          Type: S3
          Properties:
            Bucket:
              Ref: Bucket1
            Events:
              - 's3:ObjectCreated:*'
            Filter:
              S3Key:
                Rules:
                  - Name: suffix
                    Value: .json
  Bucket1:
    Type: 'AWS::S3::Bucket'
    
Step 4: To upload the below deployment package in the AWS Lambda function:

```
Deployment package location: /json_to_csv_conversion_repo/json_to_csv_conversion_repo/lambda/json_to_csv_package.zip
```

Step 5: To add an S3 bucket “json-temp-file-generator” event trigger based on 

```
Event Type: ObjectCreated				Suffix:.json

to execute Lambda function.
```
![alt text](https://github.com/tapaswivijay/json_to_csv_conversion_repo/blob/master/json_to_csv_conversion_repo/lambda/jsontocsv/Lambda_event_Trigger.PNG)

Step 6: Executing process for the First Time (First Run)

Step 6a: When a new generated JSON file is added to “json-temp-file-generator” bucket, the 

lambda function execution is triggered and the source/destination s3 buckets are created 

dynamically where the source files contains the JSON files copied from tmp S3 bucket and 

destination bucket is loaded with converted CSV files.

Temp S3 bucket:
![alt text](https://github.com/tapaswivijay/json_to_csv_conversion_repo/blob/master/json_to_csv_conversion_repo/lambda/jsontocsv/json-temp-file-generator.PNG)

S3 buckets after execution:
![alt text](https://github.com/tapaswivijay/json_to_csv_conversion_repo/blob/master/json_to_csv_conversion_repo/lambda/jsontocsv/s3_buckets_after_execution.PNG)

Source S3 bucket:
![alt text](https://github.com/tapaswivijay/json_to_csv_conversion_repo/blob/master/json_to_csv_conversion_repo/lambda/jsontocsv/json-to-csv-source.PNG)

Target S3 bucket:
![alt text](https://github.com/tapaswivijay/json_to_csv_conversion_repo/blob/master/json_to_csv_conversion_repo/lambda/jsontocsv/json-to-csv-destination.PNG)

Step 7: Subsequent executions

Step 7a: When the latest JSON file generated is uploaded to the “json-temp-file-generator” 

bucket, the lambda function execution is triggered and the latest JSON file is copied to Source S3 

bucket and corresponding converted CSV file is uploaded to destination bucket.

Temp S3 bucket:
![alt text](https://github.com/tapaswivijay/json_to_csv_conversion_repo/blob/master/json_to_csv_conversion_repo/lambda/jsontocsv/json-temp-file-generator1.PNG)

Source S3 bucket:
![alt text](https://github.com/tapaswivijay/json_to_csv_conversion_repo/blob/master/json_to_csv_conversion_repo/lambda/jsontocsv/json-to-csv-source1.PNG)

Target S3 bucket:
![alt text](https://github.com/tapaswivijay/json_to_csv_conversion_repo/blob/master/json_to_csv_conversion_repo/lambda/jsontocsv/json-to-csv-destination1.PNG)
