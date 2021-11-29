## Azure Synapse Link For Dataverse POC Environment Setup
This deployment package allows the user to deploy a Proof-of-Concept environment of Azure Synapse Analytics link for Dataverse with spark notebook to (ingest, merge, aggregate and transform) dataset.

## 1-Prerequisites

1-If you are an internal Microsoft employee, you must complete the prerequisites in this [Internal Guide](https://microsoft.sharepoint.com/:w:/t/Analytics-TechStrategy-Team/EcHBx8uEk6VOvYEUVW3m3yQBCeHwW-A3L2hzfk-ZrZmTKg?e=fY4eOy) before proceeding to next steps.

2-Owner role (or Contributor roles) for the Azure Subscription is required to deploy the template. This is for creation of a separate Proof-of-Concept Resource Group and to delegate roles necessary for this proof of concept.[How To Assign Azure Role](https://docs.microsoft.com/en-us/azure/role-based-access-control/role-assignments-portal?tabs=current).Refer to this [official documentation](https://docs.microsoft.com/en-us/azure/role-based-access-control/role-assignments-steps) for more details on RBAC role-assignments.


## 2-Access a Dynamics Trial Environment

1. Navigate to the [Dynamics 365 Trial Site](https://dynamics.microsoft.com/en-us/dynamics-365-free-trial/).
2. Select **Try for free** for *Dynamics 365 Sales*.
3. Enter your work or school email and select **Start your free trial**.For Internal Microsoft its the email from the prerequisite step.

![D365](https://github.com/Azure/Test-Drive-Synapse-Link-For-DataVerse/blob/main/images/d365-trial.gif)

4. Open a new tab and navigate to the [Power Platform Maker Portal](https://make.preview.powerapps.com/).
5. In the top right corner, select the **Environment** to open a right pane with all the environments.
6. Select **Sales Trial** from the list and then select **Dataverse**>**Azure Synapse Link** or if you see **Data**>**Azure Synapse Link**  from the left navigation pane.
7. Select **+ New link** and make note of the region required for deploying the Azure resources in the next steps.

![PPMP](https://github.com/Azure/Test-Drive-Synapse-Link-For-DataVerse/blob/main/images/ppac-video.gif)

## 3-Deployment Steps
1.  ## Fork Out [This GitHub Repository](https://github.com/Azure/Test-Drive-Synapse-Link-For-DataVerse) into your GitHub account. 
    
   **If you don't fork repo:** 
   + **The spark notebook will not be deployed**
   + **You will get a Github publishing error**
   
  <!--  ![Fork](https://raw.githubusercontent.com/Azure/Test-Drive-Synapse-Link-For-DataVerse/main/images/4.gif) -->
 
2. Click 'Deploy To Azure' button given below to deploy Synapse Workspace with SQL and Spark Pool.

    [![Deploy To Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FTest-Drive-Synapse-Link-For-DataVerse-With-1-Click%2Fmain%2Fazuredeploy.json)

   - Provide the values for:

     - Resource group (create new)
     - Region
     - Company Tla - *Any three Characters to keep the Azure resource names unique*
     - Option (true or false) for Allow All Connections - *Firewall to allow/deny connection to Synapse SQL*
     - Option (true or false) for Spark Deployment - *Synapse Spark pool, Select "True" for this pacakage deployment*
     - Spark Node Size (Small, Medium, large) *if Spark Deployment is set to true.Recomended "Small"*
     - Sql Administrator Login
     - Sql Administrator Login Password
     - Sku 
     - Option (true or false) for Metadata Sync - *Serverless SQL pool can automatically synchronize metadata from Apache Spark*
     - Frequency
     - Time Zone
     - Resume Time
     - Pause Time
     - Github Username (username for the account where [this github repository](https://github.com/Azure/Test-Drive-Synapse-Link-For-DataVerse) was forked out into)

   - Click 'Review + Create'.
   - On successful validation, click 'Create'.

#### 3A-Azure Services being deployed
This template deploys necessary resources to run an Azure Synapse Analytics link for Dataverse Proof-of-Concept. 
Following resources are deployed with this template:

- An Azure Synapse Workspace 
- An Azure Synapse SQL Pool
- Apache Spark Pool
- Azure Data Lake Storage Gen2 account
- A new File System inside the Storage Account to be used by Azure Synapse
- A Logic App to Pause the SQL Pool at defined schedule
- A Logic App to Resume the SQL Pool at defined schedule
- A key vault to store the secrets

<!-- The data pipeline inside the Synapse Workspace gets New York Taxi trip and fare data, joins them and perform aggregations on them to give the final aggregated results. Other resources include datasets, linked services and dataflows. All resources are completely parameterized and all the secrets are stored in the key vault. These secrets are fetched inside the linked services using key vault linked service. The Logic App will check for Active Queries. If there are active queries, it will wait 5 minutes and check again until there are none before pausing -->

## 4-Post Deployment
1. Current Azure user needs to have Storage Blob Data Contributor role access to recently created Azure Data Lake Storage Gen2 account to avoid 403 type permission errors. ["How to Assign Storage Blob Data Contributor Role"](https://docs.microsoft.com/en-us/azure/synapse-analytics/get-started-add-admin#azure-rbac-role-assignments-on-the-workspaces-primary-storage-account)
2. After the deployment is complete, click 'Go to resource group'.
3. You'll see all the resources deployed in the resource group.
4. Click on the newly deployed Synapse workspace.
5. Click on link 'Open' inside the box labeled as 'Open Synapse Studio'.
6. Click on 'Log into Github' after workspace is opened. Provide your credentials for the github account holding the forked out repository.
7. After logging in into your github account, click on 'Develop' icon in the left panel and then Select 'Notebooks'. A blade will appear from right side of the screen.
8. Make sure that 'main' branch is selected as 'Working branch' and click 'Save'.

![PostDeployment-1](https://github.com/Azure/Test-Drive-Synapse-Link-For-DataVerse/blob/main/images/Starting_WS.gif)

#### 4A-Configuring Synapse Link for Dataverse
1. Before starting with these steps wait around 5-10 mins after ARM template deployment. This allows Azure resources to sync up properly for the Power Platform.
2. Open a new tab and navigate to the [Power Platform Maker Portal](https://make.preview.powerapps.com/).
3. In the top right corner, select the **Environment** to open a right pane with all the environments.
4. Select **Sales Trial** from the list and then select **Dataverse**>**Azure Synapse Link** or if you see **Data**>**Azure Synapse Link** from the left navigation pane.
5. Select **+ New link** and check "Connect to Your Azure Synapse Analytics Workspace"
6. Select Subscription,Resource Group and Storage account the package was deployed in previous deployment step.Click Next.
7. Select the following tables to be exported through Synapse Link and click Save.
     - Account
     - Contact
     - Lead
     - Opportunity
8. On completion the selected tables should show up as synced.
9. Same tables can be seen and accessed thorough Synapse workspace. On the left panel click Data and then Lake Database


![Configure-Link](https://github.com/Azure/Test-Drive-Synapse-Link-For-DataVerse/blob/main/images/Configure_DV_Link.gif)


![DV_Syn_Tbl](https://github.com/Azure/Test-Drive-Synapse-Link-For-DataVerse/blob/main/images/DV_Syn_Tables.gif)


#### 4B-Synapse Studio Notebook Execution using Dataverse Tables
3. Click on the newly deployed Synapse workspace.
4. Click on link 'Open' inside the box labeled as 'Open Synapse Studio'.
6. Click on 'Develop' icon in the left panel and then Select 'Notebooks'. A blade will appear from the right side of the screen.
7. Select the Notebook 'Revenue Summary'.
8. Follow the instruction within the Notebook to proceed with the further execution steps.

![NB_Executionl](https://github.com/Azure/Test-Drive-Synapse-Link-For-DataVerse/blob/main/images/NB_Execution.gif)
