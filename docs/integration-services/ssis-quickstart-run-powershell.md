---
title: "Run an SSIS package with PowerShell | Microsoft Docs"
ms.date: "05/21/2018"
ms.topic: conceptual
ms.prod: sql
ms.prod_service: "integration-services"
ms.component: "quick-start"
ms.suite: "sql"
ms.custom: ""
ms.technology: 
  - "integration-services"
author: "douglaslMS"
ms.author: "douglasl"
manager: craigg
---
# Run an SSIS package with PowerShell
This quickstart demonstrates how to use a PowerShell script to connect to a database server and run an SSIS package.

## Supported platforms

You can use the information in this quickstart to run an SSIS package on the following platforms:

-   SQL Server on Windows.

-   Azure SQL Database. For more info about deploying and running packages in Azure, see [Lift and shift SQL Server Integration Services workloads to the cloud](lift-shift/ssis-azure-lift-shift-ssis-packages-overview.md).

You cannot use the information in this quickstart to run an SSIS package on Linux. For more info about running packages on Linux, see [Extract, transform, and load data on Linux with SSIS](../linux/sql-server-linux-migrate-ssis.md).

## PowerShell script
Provide appropriate values for the variables at the top of the following script, and then run the script to run the SSIS package.

> [!NOTE]
> The following example uses Windows Authentication. To use SQL Server authentication, replace the `Integrated Security=SSPI;` argument with `User ID=<user name>;Password=<password>;`. If you're connecting to an Azure SQL Database server, you can't use Windows authentication. 

```powershell
# Variables
$SSISNamespace = "Microsoft.SqlServer.Management.IntegrationServices"
$TargetServerName = "localhost"
$TargetFolderName = "Project1Folder"
$ProjectName = "Integration Services Project1"
$PackageName = "Package.dtsx"

# Load the IntegrationServices assembly
$loadStatus = [System.Reflection.Assembly]::Load("Microsoft.SQLServer.Management.IntegrationServices, "+
    "Version=14.0.0.0, Culture=neutral, PublicKeyToken=89845dcd8080cc91, processorArchitecture=MSIL")

# Create a connection to the server
$sqlConnectionString = `
    "Data Source=" + $TargetServerName + ";Initial Catalog=master;Integrated Security=SSPI;"
$sqlConnection = New-Object System.Data.SqlClient.SqlConnection $sqlConnectionString

# Create the Integration Services object
$integrationServices = New-Object $SSISNamespace".IntegrationServices" $sqlConnection

# Get the Integration Services catalog
$catalog = $integrationServices.Catalogs["SSISDB"]

# Get the folder
$folder = $catalog.Folders[$TargetFolderName]

# Get the project
$project = $folder.Projects[$ProjectName]

# Get the package
$package = $project.Packages[$PackageName]

Write-Host "Running " $PackageName "..."

$result = $package.Execute("false", $null)

Write-Host "Done."
```

## Next steps
- Consider other ways to run a package.
    - [Run an SSIS package with SSMS](./ssis-quickstart-run-ssms.md)
    - [Run an SSIS package with Transact-SQL (SSMS)](./ssis-quickstart-run-tsql-ssms.md)
    - [Run an SSIS package with Transact-SQL (VS Code)](ssis-quickstart-run-tsql-vscode.md)
    - [Run an SSIS package from the command prompt](./ssis-quickstart-run-cmdline.md)
    - [Run an SSIS package with C#](./ssis-quickstart-run-dotnet.md) 
