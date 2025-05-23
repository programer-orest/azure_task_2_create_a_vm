# Azure VM Deployment

This day has come — we are deploying a cloud infrastructure! In this task, you will deploy a virtual machine and configure it to host your own web application! 

## Prerequisites

Before completing any task in the module, make sure that you have followed all the steps described in the **Environment Setup** topic, in particular: 

1. Ensure you have an [Azure](https://azure.microsoft.com/en-us/free/) account and subscription.

2. Create a resource group called `mate-resources` in the Azure subscription.

3. In the mate-resources resource group, create a storage account (any name) and a task-artifacts container.

4. Install [PowerShell 7](https://learn.microsoft.com/en-us/powershell/scripting/install/installing-powershell?view=powershell-7.4) on your computer. All tasks in this module use PowerShell 7.
   Execute the following command to run it in the terminal: 
    ```
    pwsh
    ```

6. Install [Azure module for PowerShell 7](https://learn.microsoft.com/en-us/powershell/azure/install-azure-powershell?view=azps-11.3.0): 
    ```
    Install-Module -Name Az -Repository PSGallery -Force
    ```
If you are a Windows user, before running this command, please also run the following: 
    ```
    Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
    ```

6. Log in to your Azure account using PowerShell:
    ```
    Connect-AzAccount -TenantId <your Microsoft Entra ID tenant id>
    ```
## Requirements

In this task, you need to perform the following steps: 

1. Create a Virtual Machine:

    1. Create a new resource group `mate-azure-task-2`

    2. Create a Linux Virtual Machine in the resource group from the previous step: 
        
        - Use any allowed VM name
        - Use `UK South` Azure region
        - Set availability option to `No infrastructure redundancy`
        - Use `Standard` security type 
        - Use `Ubuntu Server 22.04` as an operating system (publisher — `Canonical`)
        - Use `B1s` VM size 
        - Use SSH key authentication (use your own SSH key)
        - Make sure VM is deployed **without auto-generated public IP**

    3. Create and attach a public IP with SKU `Basic`. Create a DNS label for the public IP. 

    4. Configure VM's Network Security Group to accept network connections on `22` and `8080` TCP ports. For that, create **two separate NSG rules**.

2. Deploy the web application to the virtual machine
    
    1. Connect to the VM using SSH, create a folder `/app`, and configure your user as the owner of the folder: 
        ```
            ssh <your-vm-username>@<your-public-ip-DNS-name>
            sudo mkdir /app 
            sudo chown <your-vm-username>:<your-vm-username> /app
        ```

    2. From your computer, copy the content of the folder `app` to your virtual machine (run the command in the folder of this repository): 
        
        ```
            scp -r app/* <your-vm-username>@<your-public-ip-DNS-name>:/app
        ```

    3. Connect to the virtual machine again using SSH, install pre-requirements, and configure a service for the application:
        
        ```
            sudo apt install python3-pip
            cd /app
            sudo mv todoapp.service /etc/systemd/system/ 
            sudo systemctl daemon-reload
            sudo systemctl start todoapp
            sudo systemctl enable todoapp
        ```
    
    4. Verify that the web app service is running. For that, run the following command on the VM: 
        
        ```
            systemctl status todoapp
        ```

3. Verify that the web application is running; for that, open in a web browser the following URL: `http://<your-public-ip-DNS-name>:8080`. You should see the main page of the todo app. 

4. Run artifacts generation script `scripts/generate-artifacts.ps1`.

5. Test yourself using the script `scripts/validate-artifacts.ps1`.

6. Submit the solution for review.

7. When the solution is validated, stop the virtual machine.
   
## How to Complete Tasks in This Module 

Tasks in this module rely on 2 PowerShell scripts: 

- `scripts/generate-artifacts.ps1` generates the task “artifacts” and uploads them to cloud storage. An “artifact” is evidence of a task completed by you. Each task will have its own script, which will gather the required artifacts. The script also adds a link to the generated artifact in the `artifacts.json` file in this repository — make sure to commit changes to this file after you run the script. 
- `scripts/validate-artifacts.ps1` validates the artifacts generated by the first script. It loads information about the task artifacts from the `artifacts.json` file.

Here is how to complete tasks in this module:

1. Clone the task repository.

2. Make sure you have completed the steps described in the Prerequisites section.

3. Complete the task described in the Requirements section.

4. Run `scripts/generate-artifacts.ps1` to generate task artifacts. The script will update the file `artifacts.json` in this repo. 

5. Run `scripts/validate-artifacts.ps1` to test yourself. If tests are failing, follow the recommendation from the test script error message to fix or re-deploy your infrastructure. When you are ready to test yourself again — **re-generate the artifacts** (step 4) and re-run the tests. 

6. When all tests pass, commit your changes and submit the solution for review.

7. To complete this task, just add a valid link to the `artifacts.json` file and commit the changes.
