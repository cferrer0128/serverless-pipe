# serverless-pipe

### Pipeline
The pipeline is a full CI/CD serverless pipeline for building and deploying your api. In this example we are using CloudFormation to create the pipeline, all resources, and any permissions needed.

The following resources are created:

- An S3 bucket to store deployment artifacts.
- An AWS CodeBuild stage to build any changes checked into the repo.
- The AWS CodePipeline that will watch for changes on your repo, and push these changes through to build and deployment steps.
- All IAM roles and policies required.

The CloudFormation templates being used to create these resources can be found in [pipeline directory](pipeline/).

To create the pipeline stack, click the launch stack button below.

[<img src="https://s3.amazonaws.com/cloudformation-examples/cloudformation-launch-stack.png">](https://console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks/new?stackName=myteststack&templateURL=https://awscomputeblogimages.s3-us-west-2.amazonaws.com/samfarm-main.yaml)

### Update your website
If you have looked at the code in the website (or if you haven't, now is your chance!), you may have noticed that the website makes http requests to an api periodically to update the number of SAMs on the screen. Right now it is pointing at nothing, so lets update it to point it to brand spanking new API. In the [app.js](website/app.js), look for the line

```javascript
var GET_SAM_COUNT_URL = 'INSERT API GATEWAY URL HERE';
```

and update that to your API Gateway endpoint. It should be in the format:

```
https://<api-id>.execute-api.us-east-1.amazonaws.com/Prod/sam
```

Now lets update your S3 static website with this change:

```bash
sh upload_website.sh <s3-bucket-name>
```


## Step 5
### Start the party (or how I learned to stop worrying and push a change)
Now that we have our website, our code repository with our lambda function and our pipeline configured, lets see it in action. We are going to make two changes to our repository, first were going to setup our API for CORS, and second were going to update our Lambda function. Both changes will be made in the repo you created in Step 2.


#### CORS
Go to the beta.json file and update the following line:

```json
"OriginUrl": "*"
```

to the url for the S3 static site we created. Something like:

```json
"OriginUrl": "http://<s3-bucket-name>.s3-website-us-east-1.amazonaws.com"
```


#### Lambda Function
Go to index.js file the repo you made in Step 2 and update the line:

```javascript
var samCount = 1;
```

to

```javascript
var samCount = 15;
```

Commit and push the changes. 

Go back to the pipeline we generated in Step 3, you will see AWS CodePipeline automatically pick up your change, and start the build and deploy process. Voila! A completely version controlled, serverless, CI/CD solution to give a squirrel a few friends. Technology!
