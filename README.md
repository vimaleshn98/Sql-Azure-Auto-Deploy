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
      
 
   
 
   
  
