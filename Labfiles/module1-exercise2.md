### HOL1: Exercise 2: Set up your environment on Azure to migrate servers

### Task 1: Create a migration assessment

In this task, you will use Azure Migrate to create a migration assessment for the SmartHotel application, using the data gathered during the discovery phase.

1. Select **Assess** under **Azure Migrate: Discovery and assessment** and click on **Azure VM** to start a new migration assessment.

   ![Screenshot of the Azure Migrate portal blade, with the '+Assess' button highlighted.](Images/start-assess-v2.png "Start assessment")

2. On the Assess servers blade, ensure the Assessment type to be **Azure VM** and Discovery Source to be **Servers discovered from Migrate Appliance**. Under **Assessment settings**, select **Edit**.

   ![Screenshot of the Azure Migrate 'Assess servers' blade, showing the assessment name.](Images/assessment1.png "Assess servers - assessment name")

3. The **Assessment settings** blade allows you to tailor many of the settings used when making a migration assessment report. Take a few moments to explore the wide range of assessment properties. Hover over the information icons to see more details on each setting. Choose any settings you like, then select **Save**. (You have to make a change for the Save button to be enabled; if you don't want to make any changes, just close the blade.)

   ![Screenshot of the Azure Migrate 'Assessment properties' blade, showing a wide range of migration assessment settings.](Images/assessment2.png "Assessment properties")

4. Select **Next** to move to the **Select servers to assess** tab and enter the following information:
     
     1. Assessment name: Enter **SmartHotelAssessment** 
     1. Select or create a group: Choose **Create New** and enter the 
     1. Group name: Enter **SmartHotel VMs**.
     1. Add machines to the Group:  Select **SmarthotelAppl** from dropdown.
     1. Select the **smarthotelweb1**, **smarthotelweb2** and **UbuntuWAF** VMs and
     1.  Click on **Next**.

   ![Screenshot of the Azure Migrate 'Assess servers' page. A new server group containing servers smarthotelweb1, smarthotelweb2, and UbuntuWAF.](Images/create%20assessment.png "Assessment VM group")

    **Note:** There is no need to include the **smarthotelSQL1** or **AzureMigrateAppliance** VMs in the assessment, since they will not be migrated to Azure. (The SQL Server will be migrated to the SQL Database service and the Azure Migrate Appliance is only used for migration assessment.)

5. Click on **Create assessment** to create the assessment. 

   ![](Images/create%20assessment1.png)

6. On the **Servers, databases, and web apps** blade, select **Refresh** periodically until the number of assessments shown is **1** (This may take few minutes). Once the assessments count is updated, click on **1** that is next to **Total** under **Assessments**.  

    ![Screenshot from Azure Migrate showing the number of assessments as '1'.](Images/E1T4S6.png "Azure Migrate - Assessments (count)")
    
7. Select **Assessments** under **Azure Migrate: Discovery and assessment** to see a list of assessments. Then select the actual assessment.

   ![Screenshot showing a list of Azure Migrate assessments. There is only one assessment in the list. It has been highlighted.](Images/assessment-list-v2.png "Azure Migrate - Assessments (list)")

### Task 2: Configure dependency visualization

When migrating a workload to Azure, it is important to understand all workload dependencies. A broken dependency could mean that the application does not run properly in Azure, perhaps in hard-to-detect ways. Some dependencies, such as those between application tiers, are obvious. Other dependencies, such as DNS lookups, Kerberos ticket validation or certificate revocation checks, are not.

In this task, you will configure the Azure Migrate dependency visualization feature. This requires you to first create a Log Analytics workspace, and then to deploy agents on the to-be-migrated VMs.

1. Return to the **Azure Migrate** blade in the Azure Portal, select **Servers, databases and web apps (1)**. Under **Discovery and assessment** select **Groups (2)**,

    ![](Images/E1T5S1.png)   

2. Select the **SmartHotel VMs** group to see the group details. 

   ![](Images/select-group.png)   

3. Note that each VM has their **Dependencies** status as **Requires agent installation**. Select **Requires agent installation** for the **smarthotelweb1** VM.

   ![Screenshot showing the SmartHotel VMs group. Each VM has dependency status 'Requires agent installation'.](Images/requires-agent-installation-v2.png "SmartHotel VMs server group")

4. On the **Dependencies** blade, select **Configure Log Analytics workspace**.

   ![Screenshot of the Azure Migrate 'Dependencies' blade, with the 'Configure OMS Workspace' button highlighted.](Images/configureLAW.png "Configure OMS Workspace link")

5. On the **Configure Log Analytics workspace** blade, provide the below information and select **Configure**.

   - Log Analytics workspace: Click on **Create new** and enter **AzureMigrateWS<inject key="DeploymentID" enableCopy="false" />**
   - Log Analytics workspace location: Select **East US** from the dropdown.

  ![Screenshot of the Azure Migrate 'Configure OMS workspace' blade.](Images/createLAW.png "OMS Workspace settings")

6. Wait for the workspace to be deployed. Once it is deployed, navigate to **AzureMigrateWS<inject key="DeploymentID" enableCopy="false" />** by clicking on it.

   ![Screenshot of the Azure Migrate 'Configure OMS workspace' blade.](Images/omsworkspace.png "OMS Workspace settings")

7. Select **Agents management** under **Settings** from the left-hand side menu. Make a note of the **Workspace ID** and **Primary Key** (for example by using Notepad).

   ![Screenshot of part of the Azure Migrate 'Dependencies' blade, showing the OMS workspace ID and key.](Images/workspace-id-key.png "OMS Workspace ID and primary key")

8. Return to the Azure Migrate **Dependencies** blade. Copy each of the 4 agent download URLs and paste them alongside the Workspace ID and key you noted in the previous step. 
   
   ![Screenshot of the Azure Migrate 'Dependencies' blade with the 4 agent download links highlighted.](Images/agent-links.png "Agent download links")

9. From **Hyper-V Manager** console, select **smarthotelweb1** and select **Connect**.

   ![Screenshot from Hyper-V manager highlighting the 'Connect' button for the smarthotelweb1 VM.](Images/Hyperv4.png "Connect to smarthotelweb1")

10. Select **Connect** again when prompted and log in to the **Administrator** account using the password **<inject key="SmartHotelHost Admin Password" />**.

11. Go to **Start** button in the **smarthotelweb1** VM and select **Internet Explorer** to open it. Paste the link to the 64-bit Microsoft Monitoring Agent for Windows, which you noted earlier. When prompted, **Run** the installer.

    > **Note:** You may need to disable **Internet Explorer Enhanced Security Configuration** on **Server Manager** under **Local Server** to complete the download. 

    ![Screenshot showing the Internet Explorer prompt to run the installer for the Microsoft Monitoring Agent.](Images/mma-win-run.png "Run MMA installer")

12. On the **Welcome to the Microsoft Monitoring Agent Setup Wizard** blade, select **Next**. 

    ![Screenshot for installing 64-bit Microsoft Monitoring Agent for Windows.](Images/mma1.png "MMA installation")

13. On the **Microsoft Software License Terms** blade, select **I Agree** 

    ![Screenshot for installing 64-bit Microsoft Monitoring Agent for Windows.](Images/mma2.png "MMA installation")

14. On the **Destination Folder** blade, leave everything as default and select **Next**. 

    ![Screenshot for installing 64-bit Microsoft Monitoring Agent for Windows.](Images/mma3.png "MMA installation") 

15. On the **Agent Setup Options** blade, select **Connect the agent to Azure Log Analytics (OMS)** and select **Next**.

    ![Screenshot for installing 64-bit Microsoft Monitoring Agent for Windows.](Images/mma4.png "MMA installation") 

16. On the **Azure Log Analytics** blade, enter the Workspace ID and Workspace Key that you copied earlier, and select **Azure Commercial** from the Azure Cloud drop-down then select **Next**.

    ![Screenshot of the Microsoft Monitoring Agent install wizard, showing the Log Analytics (OMS) workspace ID and key.](Images/mma-wizard.png "MMA agent installer - workspace configuration")

17. On the **Microsoft Update** blade, leave everything as default and select **Next**. 

    ![Screenshot for installing 64-bit Microsoft Monitoring Agent for Windows.](Images/mma7.png "MMA installation")

18. On the **Ready to Install** blade, click on **Install**. 

    ![Screenshot for installing 64-bit Microsoft Monitoring Agent for Windows.](Images/mma5.png "MMA installation")

19. Select **Finish** to finish the installation process of **Microsoft Monitoring Agent for Windows**.

    ![Screenshot for installing 64-bit Microsoft Monitoring Agent for Windows.](Images/mma6.png "MMA installation")

20. Paste the link to the Dependency Agent Windows installer into the browser address bar. **Run** the installer.

    ![Screenshot showing the Internet Explorer prompt to run the installer for the Dependency Agent.](Images/da-win-run.png "Run Dependency Agent installer")

21. On the **License Agreement** blade, select **I Agree** to accept the agreement and continue. 

    ![Screenshot for installing Dependency Agent.](Images/dependencyagent1.png "Dependency Agent installation") 

22. On the **Completing Dependency Agent Setup** blade, select **Finish** to finish the installation process.

    ![Screenshot for installing Dependency Agent.](Images/dependencyagent2.png "Dependency Agent installation") 
 

   > **Note:** You do not need to configure the workspace ID and key when installing the Dependency Agent, since it uses the same settings as the Microsoft Monitoring Agent, which must be installed beforehand.

23. Close the virtual machine connection window for the **smarthotelweb1 VM**. Connect to the **smarthotelweb2 VM** and repeat the installation process (steps 10-22) for both agents (the administrator password is the same as for smarthotelweb1). Close the virtual machine connection window for the **smarthotelweb2 VM**, once the installation of agents is done.

24. Return to the Azure Portal and refresh the Azure Migrate **SmartHotel VMs** VM group blade. The 3 VMs on which the dependency agent was installed should now show their status as **Installed**. (If not, refresh the page **using the browser refresh button**, not the refresh button in the blade.  It may take up to **5 minutes** after installation for the status to be updated.)

   ![Screenshot showing the dependency agent installed on each VM in the Azure Migrate VM group.](Images/dependency-viz-installed.png "Dependency agent installed")
   
   >**Note**: If you notice that the dependency agent status is showing as **Requires Agent Installation** instead of Installed even after installing dependency agents in all the three VMs, please follow the steps from [here](https://github.com/CloudLabsAI-Azure/Know-Before-You-Go/blob/main/AIW-KBYG/AIW-Infrastructure-Migration.md#4-exercise1---task6---step1) to confirm dependency agent installation in VMs using Log Analytics workspace.
 
25. Select **View dependencies**.

   ![Screenshot showing the view dependencies button in the Azure Migrate VM group blade.](Images/view-dependencies.png "View dependencies")
   
26. Take a few minutes to explore the dependencies view. Expand each server to show the processes running on that server. Select a process to see process information. See which connections each server makes.

    ![Screenshot showing the dependencies view in Azure Migrate.](Images/dependencies1.png "Dependency map")
    