On the project configuration page, under Source Code Management, choose Git. For Repository URL, enter the URL of your GitHub repository.
.

.
For Build Triggers, select the Poll SCM check box. In the Schedule, for testing enter H/2 * * * *. This entry tells Jenkins to poll GitHub every two minutes for updates.
.

.
Under Build Environment, select the Delete workspace before build starts check box. Each Jenkins project has a dedicated workspace directory. This option allows you to wipe out your workspace directory with each new Jenkins build, to keep it clean.
.

.
Under Build Actions, add a Build Step, and AWS CodeBuild. On the AWS Configurations, choose Manually specify access and secret keys and provide the keys.
.
.
From the CloudFormation stack Outputs tab, copy the AWS CodeBuild project name (myProjectName) and paste it in the Project Name field. Also, set the Region that you are using and choose Use Jenkins source.
It is a best practice is to store AWS credentials for CodeBuild in the native Jenkins credential store. For more information, see the Jenkins AWS CodeBuild Plugin wiki.
.
.
To make sure that all files cloned from the GitHub repository are deleted choose Add build step and select File Operation plugin, then click Add and select File Delete. Under File Delete operation in the Include File Pattern, type an asterisk.
.
.
Under Build, configure the following:
Choose Add a Build step.
Choose HTTP Request.
Copy the S3 bucket name from the CloudFormation stack Outputs tab and paste it after (http://s3-eu-central-1.amazonaws.com/) along with the name of the zip file codebuild-artifact.zip as the value for HTTP Plugin URL.
Example: (http://s3-eu-central-1.amazonaws.com/mybucketname/codebuild-artifact.zip)
For Ignore SSL errors?, choose Yes.
.

.
Under HTTP Request, choose Advanced and leave the default values for Authorization, Headers, and Body. Under Response, for Output response to file, enter the codebuild-artifact.zip file name.
.

.
Add the two build steps for the File Operations plugin, in the following order:
Unzip action: This build step unzips the codebuild-artifact.zip file and places the contents in the root workspace directory.
File Delete action: This build step deletes the codebuild-artifact.zip file, leaving only the source bundle contents for deployment.
.
.
On the Post-build Actions, choose Add post-build actions and select the Deploy an application to AWS CodeDeploy check box.
Enter the following values from the Outputs tab of your CloudFormation stack and leave the other settings at their default (blank):
For AWS CodeDeploy Application Name, enter the value of CodeDeployApplicationName.
For AWS CodeDeploy Deployment Group, enter the value of CodeDeployDeploymentGroup.
For AWS CodeDeploy Deployment Config, enter CodeDeployDefault.OneAtATime.
For AWS Region, choose the Region where you created the CodeDeploy environment.
For S3 Bucket, enter the value of S3BucketName.
The CodeDeploy plugin uses the Include Files option to filter the files based on specific file names existing in your current Jenkins deployment workspace directory. The plugin zips specified files into one file. It then sends them to the location specified in the S3 Bucket parameter for CodeDeploy to download and use in the new deployment.
.
As shown below, in the optional Include Files field, I used (**) so all files in the workspace directory get zipped.
.
.
Choose Deploy Revision. This option registers the newly created revision to your CodeDeploy application and gets it ready for deployment.
Select the Wait for deployment to finish? check box. This option allows you to view the CodeDeploy deployments logs and events on your Jenkins server console output.
.
.
Now that you have created a project, you are ready to test deployment.