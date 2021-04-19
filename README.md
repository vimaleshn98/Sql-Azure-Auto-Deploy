# Sql-Azure-Auto-Deploy

**Using Poweshell Script:**
  Azure PowerShell is a set of cmdlets for managing Azure resources directly from the PowerShell command line.
  Azure CLI, PowerShell, ARM Template, or the portal.

  In sql-powershell.ps1 mentioned how we can deploy the sql-server on azure. this is powershell script mainly used on auto deploying of resoure on azure.
  
  Import-Module Az 
    importing az module for interacting with azure.
  Connect-AzAccount
    This is used to connect azure acount, on which we want to deploy our resorces. we can add credential here for connecting. i have not mention because my ps is already connect       with az-account.
  Variable: variable are assigned the values which we are going to use on azuredeployment.
  
  Before Creating the Sql-server we need to create the resource group.
  
  Resource Group : It's a Logical container on which we are going put all the resource for that particular project. location of RG and Resource inside that might be different. It      help us to create separate container so we can manage life-cycle of resource easily. 
     
     New-AzResourceGroup -Name $resourceGroupName -Location $location -Tag @{Owner="SQLDB-Samples")
     here New-AzResourceGroup is module used to create new resource group, -tag is used for identification.
 
 Once Resource group is create we can add all resource inside that. including depending resources.
     
     New-AzSqlServer -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -Location $location `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
      -ArgumentList $adminLogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
 this cmdlets used for creating the sql-server with name,location ,and credential(creating new pscredential object with parameter username and password)

Setting up the Firewall rule for sql Server:
  
    New-AzSqlServerFirewallRule -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
       -FirewallRuleName "AllowedIPs" -StartIpAddress $startIp -EndIpAddress $endIp
 
 This cmdlets used for crate firewall rule with "allowed ip" within range specified.  
 So now we are created the Sql-server with firewall enabled.

create Database now:
 
    New-AzSqlDatabase  -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -DatabaseName $databaseName `
      -Edition GeneralPurpose `
      -ComputeModel Serverless `
      -ComputeGeneration Gen5 `
      -VCore 2 `
      -MinimumCapacity 2 `
      -SampleName "AdventureWorksLT"
      
 
**Using ARM Templates**
  One more approach for deploying  Resource on the azure is using ARM template.
  If you need a way of deploying infrastructure-as-code to Azure, then Azure Resource Manager (ARM) Templates are the obvious way of doing it.They define the objects you want,   their types, names and properties in a JSON file which can be understood by the ARM API. Azure is managed using an API: Originally it was managed using the Azure Service       management API or ASM which control deployments of what is termed “Classic”. This was replaced by the Azure Resource Manager or ARM API. 
  An ARM template can either contain the contents of an entire resource group or it can contain one or more resources from a resource group. 
  
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#"
  "contentVersion": "1.0.0.0",
  used to specify the which schema is used for  resource templates. Versioning of the Arm template.
  
  Then comes the Parameters where can given some parameter values so the value can be passed during the execution of templates. 
  
  Then we can specify the value of variables. that we are going use on the template.
  
  then we can specify the resources which we want to deploy on azure.
      
  
  "resources": [
    {
      "type": "Microsoft.Sql/servers",
      "apiVersion": "2020-02-02-preview",
      "name": "[parameters('serverName')]",
      "location": "[parameters('location')]",
      "properties": {
        "administratorLogin": "[parameters('administratorLogin')]",
        "administratorLoginPassword": "[parameters('administratorLoginPassword')]"
      },
      "resources": [
        {
          "type": "databases",
          "apiVersion": "2020-08-01-preview",
          "name": "[parameters('sqlDBName')]",
          "location": "[parameters('location')]",
          "sku": {
            "name": "Standard",
            "tier": "Standard"
          },
          "dependsOn": [
            "[resourceId('Microsoft.Sql/servers', concat(parameters('serverName')))]"
          ]
        }
        
  here we r specifying the types of resource and apiversion and properties of resources.
        
  At end we can use  output module for getting return value from the azure deplyments.
        
        
        
  Now we can deploy template using azure cli or powershell .
   
  
