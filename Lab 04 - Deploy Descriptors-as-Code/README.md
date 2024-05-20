<h1>Lab 4 - Deploy Descriptors-as-Code</h1>
This exercise we will walk through:

1. View and modify a transformation
2. Build and deploy a flow using a deploy descriptor
3. Export flow from your environment

**Step 1 - View and modify a transformation**

Control-M Automation API enables you to use Deploy Descriptor to change job definition properties in JSON format before building, deploying, or running the JSON file. Supported changes include adding, deleting, or modifying properties or objects in the JSON file.

Using the Deploy Descriptor enables you to streamline your code across different environments (for example, between Development, Test, or Production). Since each Control-M environment has different properties, the Deploy Descriptor enables you to programmatically manipulate these properties.

Open "Lab 04 - Deploy Descriptor-as-Code\Deploy Descriptor" and close all other IDE windows. Then in the folder tree, right click on Deploy Descriptor, select "Open in Ingetrated Terminal". This will ensure you are in the correct directory folder to run your commands from the command line.

Open up the DeployDescriptor and you should see following code:

```
{
  "DeployDescriptor":
  [
    {
      "Property":"ControlmServer",
      "Source": "@",
      "Assign":"bdcctm"
    },
    {
      "Property":"Host",
      "Source": "@",
      "Assign":"bdcctm"
    },
    {
      "Property":"Application",
      "Source": "@",
      "Assign":"user01"
    },
    {
      "Property":"SubApplication",
      "Source": "@",
      "Assign":"user01_LAB04"
    },
    {
      "Property":"RunAs",
      "Source": "@",
      "Assign":"user01"
    },
    {
      "Property":"SiteStandard",
      "Source":"@",
      "Assign":"techday_std"
    },
    {
      "Property": "@",
      "ApplyOn": {
            "Type": "Folder"
            },
            "Replace": [{
               "<USER>_FIRST_JOB_FLOW_(.*)": "user01_FIRST_JOB_FLOW_PRD"
            }]
    },
    {
      "Property": "@",
      "ApplyOn": {
            "Type": "Job"
            },
            "Replace": [{
               "(.*)UAT": "$1PRD"
            }]
    },
    {
      "Property": "*",
      "Replace": [{
            "(.*)UAT": "$1PRD"
      }]
    }

  ]
}
```

The instructor will now explain the definition in detail and how it will transform the standard code from UAT to Production (PRD).

**Step 2 - Build and Deploy the Workflow Using the deploy descriptor**

For this you will need to have access to the terminal window in the IDE or the command prompt. You will need to navigate to the "Lab 04 - Deploy Descriptors-As-Code"" excercise directory.
You will then need to run the following command:

`ctm deploy transform LAB04_MY_FIRST_WORKFLOW.json DeployDescriptor.json`

Observe the output, then run:

`ctm deploy LAB04_MY_FIRST_WORKFLOW.json DeployDescriptor.json`

This will validate, build and deploy the code transforming the values in the LAB04_MY_FIRST_WORKFLOW.json file with those that have been defined in the DeployDescriptor.json. That is it will assign the hostname and all other variables to LAB04_MY_FIRST_WORKFLOW.json.

You should be able to view your workflow via the Control-M Web GUI, Planning domain. From here you will be able to submit the workflow for execution. The workflow can be monitored via the Monitoring Domain in the Control-M GUI.

**Step 3 - Export flow from your environment**

In some cases, operational or developer teams may add workflows using the simple drag and drop Control-M Web GUI.
These can then be saved and checked in to the Control-M environment.
These workflows may then be extracted from the Control-M environment to be updated or saved in a GitHub environment. In order to extract the code, you will need to use the command line or the terminal window in your IDE.

To extract the workflow we just deployed, use the following command via the command prompt or terminal window:


`ctm deploy jobs::get -s "ctm=*&folder=user01_FIRST_JOB_FLOW_PRD"`

This will produce the following output:

```
{
  "user01_FIRST_JOB_FLOW_PRD": {
    "Type": "Folder",
    "ControlmServer": "bdcctm",
    "RunAs": "user01",
    "SiteStandard": "techday_std",
    "SubApplication": "user01_LAB04",
    "Application": "user01",
    "EchoJob_PRD": {
      "Type": "Job:Command",
      "SubApplication": "user01_LAB04",
      "Host": "bdcctm",
      "Description": "Basic command job",
      "RunAs": "user01",
      "Application": "user01",
      "Command": "echo hello"
    },
    "ShowFiles_PRD": {
      "Type": "Job:Command",
      "SubApplication": "user01_LAB04",
      "Host": "bdcctm",
      "RunAs": "user01",
      "Application": "user01",
      "Command": "ls -la"
    },
    "PrintCurrentDirectory_PRD": {
      "Type": "Job:Command",
      "SubApplication": "user01_LAB04",
      "Host": "bdcctm",
      "RunAs": "user01",
      "Application": "user01",
      "Command": "pwd"
    },
    "WaitJob_PRD": {
      "Type": "Job:Command",
      "SubApplication": "user01_LAB04",
      "Host": "bdcctm",
      "RunAs": "user01",
      "Application": "user01",
      "Command": "sleep 30"
    },
    "user01_SLA_JOB_PRD": {
      "Type": "Job:SLAManagement",
      "ServiceName": "USER01_SLA_JOB_PRD",
      "ServicePriority": "3",
      "SubApplication": "user01_LAB04",
      "Host": "bdcctm",
      "JobRunsDeviationsTolerance": "3",
      "RunAs": "user01",
      "Application": "user01",
      "CompleteIn": {
        "Time": "0:3"
      }
    },
    "flow1": {
      "Type": "Flow",
      "Sequence": [
        "EchoJob_PRD",
        "ShowFiles_PRD",
        "PrintCurrentDirectory_PRD"
      ]
    },
    "flow2": {
      "Type": "Flow",
      "Sequence": [
        "EchoJob_PRD",
        "WaitJob_PRD",
        "PrintCurrentDirectory_PRD"
      ]
    },
    "flow3": {
      "Type": "Flow",
      "Sequence": [
        "PrintCurrentDirectory_PRD",
        "user01_SLA_JOB_PRD"
      ]
    }
  }
}

```
This can then be redirected to a flat file and then uploaded into your source control environment for later use.

This concludes the workshop. We hope you have found this useful. If you have any questions then please reach out to your instructors.

The video below show what we have covered:

1. Created a first workflow - 4 jobs
2. Added an SLA job at the end of the workflow
3. Finally converted the UAT jobs over to PRD

![OverviewVideo](images/OverviewVideo.gif)
