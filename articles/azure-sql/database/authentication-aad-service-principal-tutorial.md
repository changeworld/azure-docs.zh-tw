---
title: 使用服務主體建立 Azure AD 使用者
description: 此教學課程將逐步引導您在 Azure SQL Database 和 Azure Synapse Analytics 中，使用 Azure AD 應用程式 (服務主體) 建立 Azure AD 使用者
ms.service: sql-database
ms.subservice: security
ms.custom: azure-synapse
ms.topic: tutorial
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 07/27/2020
ms.openlocfilehash: 10d1fe9e47b7a3bf2d921f86703d5ae6d067813c
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87293290"
---
# <a name="tutorial-create-azure-ad-users-using-azure-ad-applications"></a>教學課程：使用 Azure AD 應用程式建立 Azure AD 使用者

[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

> [!NOTE]
> 此文章目前為**公開預覽狀態**。 如需詳細資訊，請參閱[使用 Azure SQL 的 Azure Active Directory 服務主體](authentication-aad-service-principal.md)。 此文章將使用 Azure SQL Database 來示範必要的教學課程步驟，但同樣適用於 [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)。

此文章將逐步引導您使用 Azure 服務主體 (Azure AD 應用程式)，在 Azure SQL Database 中建立 Azure AD 使用者的程序。 此功能已經存在於 Azure SQL 受控執行個體中，但現在已在 Azure SQL Database 和 Azure Synapse Analytics 中引進。 為了支援此案例，必須產生 Azure AD 身分識別，並將其指派給 Azure SQL 邏輯伺服器。

如需針對 Azure SQL 進行 Azure AD 驗證的詳細資訊，請參閱[使用 Azure Active Directory 驗證](authentication-aad-overview.md)。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> - 將身分識別指派給 Azure SQL 邏輯伺服器
> - 將目錄讀取者權限指派給 SQL 邏輯伺服器身分識別
> - 在 Azure AD 中建立服務主體 (Azure AD 應用程式)
> - 在 Azure SQL Database 中建立服務主體使用者
> - 使用 Azure AD 服務主體使用者，在 SQL Database 中建立不同的 Azure AD 使用者

## <a name="prerequisites"></a>必要條件

- 現有的 [Azure SQL Database](single-database-create-quickstart.md) 或 [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) 部署。 我們假設您在此教學課程中有一個可運作的 SQL Database。
- 可存取已經存在的 Azure Active Directory。
- 使用 PowerShell 以針對 Azure SQL 將個別 Azure AD 應用程式設定為 Azure AD 管理員時，需要 [Az.Sql 2.9.0](https://www.powershellgallery.com/packages/Az.Sql/2.9.0) \(英文\) 模組或更新版本。 確定您已升級到最新模組。

## <a name="assign-an-identity-to-the-azure-sql-logical-server"></a>將身分識別指派給 Azure SQL 邏輯伺服器

1. 連線到您的 Azure Active Directory。 您將需要尋找您的租用戶識別碼。 前往 [Azure 入口網站](https://portal.azure.com)，然後前往您的 **Azure Active Directory** 資源，即可找到此項。 在 [概觀] 窗格中，應該會看到您的**租用戶識別碼**。 執行下列 PowerShell 命令：

    - 使用您的**租用戶識別碼**取代 `<TenantId>`。

    ```powershell
    Connect-AzAccount -Tenant <TenantId>
    ```

    記錄 `TenantId`，以供未來在此教學課程中使用。

1. 產生 Azure AD 身分識別並指派給 Azure SQL 邏輯伺服器。 執行下列 PowerShell 命令：

    - 使用您的資源取代 `<resource group>` 和 `<server name>`。 如果您的伺服器名稱是 `myserver.database.windows.net`，使用 `myserver` 取代 `<server name>`。

    ```powershell
    Set-AzSqlServer -ResourceGroupName <resource group> -ServerName <server name> -AssignIdentity
    ```

    如需詳細資訊，請參閱 [Set-AzSqlServer](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlserver) \(英文\) 命令。

    > [!IMPORTANT]
    > 如果已針對 Azure SQL 邏輯伺服器設定 Azure AD 身分識別，則必須將[**目錄讀取者**](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#directory-readers)權限授與身分識別。 我們將在下一節逐步解說此步驟。 **請勿**跳過此步驟，因為 Azure AD 驗證將停止運作。

    - 如果您過去已使用 [New-AzSqlServer](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlserver) \(英文\) 命令搭配 `AssignIdentity` 參數來建立新的 SQL 伺服器，則您之後將需要以個別命令形式執行 [Set-AzSqlServer](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlserver) \(英文\) 命令，在 Azure 網狀架構中啟用此屬性。

1. 檢查是否已成功指派伺服器身分識別。 執行下列 PowerShell 命令：

    - 使用您的資源取代 `<resource group>` 和 `<server name>`。 如果您的伺服器名稱是 `myserver.database.windows.net`，使用 `myserver` 取代 `<server name>`。
    
    ```powershell
    $xyz = Get-AzSqlServer  -ResourceGroupName <resource group> -ServerName <server name>
    $xyz.identity
    ```

    您的輸出應該會顯示 `PrincipalId`、`Type` 和 `TenantId`。 指派的身分識別是 `PrincipalId`。

1. 您也可以前往 [Azure 入口網站](https://portal.azure.com)來檢查身分識別。

    - 在 [Azure Active Directory] 資源底下，前往 [企業應用程式]。 輸入您的 SQL 邏輯伺服器名稱。 您將會看到其具有一個已附加至資源的**物件識別碼**。
    
    :::image type="content" source="media/authentication-aad-service-principals-tutorial/enterprise-applications-object-id.png" alt-text="object-id":::

## <a name="assign-directory-readers-permission-to-the-sql-logical-server-identity"></a>將目錄讀取者權限指派給 SQL 邏輯伺服器身分識別

若要讓 Azure AD 指派的身分識別能夠針對 Azure SQL 正常運作，就必須將 Azure AD `Directory Readers` 權限授與伺服器身分識別。

若要授與此必要權限，請執行下列指令碼。

> [!NOTE] 
> 此指令碼必須由 Azure AD `Global Administrator` 或 `Privileged Roles Administrator` 執行。

- 使用稍早蒐集的 `TenantId` 取代 `<TenantId>`。
- 使用您自己的 SQL 邏輯伺服器名稱取代 `<server name>`。 如果您的伺服器名稱是 `myserver.database.windows.net`，使用 `myserver` 取代 `<server name>`。

```powershell
# This script grants Azure “Directory Readers” permission to a Service Principal representing the Azure SQL logical server
# It can be executed only by a "Global Administrator" or “Privileged Roles Administrator” type of user.
# To check if the “Directory Readers" permission was granted, execute this script again

Import-Module AzureAd
connect-azuread -TenantId "<TenantId>"     #Enter your actual TenantId
 $AssignIdentityName = "<server name>"     #Enter Azure SQL logical server name
 
# Get Azure AD role "Directory Users" and create if it doesn't exist
$roleName = "Directory Readers"
$role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq $roleName}
if ($role -eq $null) {
    # Instantiate an instance of the role template
    $roleTemplate = Get-AzureADDirectoryRoleTemplate | Where-Object {$_.displayName -eq $roleName}
    Enable-AzureADDirectoryRole -RoleTemplateId $roleTemplate.ObjectId
    $role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq $roleName}
}
 
# Get service principal for managed instance
$roleMember = Get-AzureADServicePrincipal -SearchString $AssignIdentityName
$roleMember.Count
if ($roleMember -eq $null)
{
    Write-Output "Error: No Service Principals with name '$    ($AssignIdentityName)', make sure that AssignIdentityName parameter was     entered correctly."
    exit
}
if (-not ($roleMember.Count -eq 1))
{
    Write-Output "Error: More than one service principal with name pattern '$    ($AssignIdentityName)'"
    Write-Output "Dumping selected service principals...."
    $roleMember
    exit
}
 
# Check if service principal is already member of readers role
$allDirReaders = Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
$selDirReader = $allDirReaders | where{$_.ObjectId -match     $roleMember.ObjectId}
 
if ($selDirReader -eq $null)
{
    # Add principal to readers role
    Write-Output "Adding service principal '$($msName)' to     'Directory Readers' role'..."
    Add-AzureADDirectoryRoleMember -ObjectId $role.ObjectId -RefObjectId     $roleMember.ObjectId
    Write-Output "'$($AssignIdentityName)' service principal added to     'Directory Readers' role'..."
 
    #Write-Output "Dumping service principal '$($AssignIdentityName)':"
    #$allDirReaders = Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
    #$allDirReaders | where{$_.ObjectId -match $roleMember.ObjectId}
}
else
{
    Write-Output "Service principal '$($AssignIdentityName)' is already member of 'Directory Readers' role'."
}
```

> [!NOTE]
> 上述這個指令碼的輸出將指出是否已將目錄讀取者權限授與該身分識別。 如果您不確定是否已授與權限，則可重新執行指令碼。

如需如何針對 SQL 受控執行個體設定**目錄讀取者**權限的類似方法，請參閱[佈建 Azure AD 管理員 (SQL 受控執行個體)](authentication-aad-configure.md#powershell)。

## <a name="create-a-service-principal-an-azure-ad-application-in-azure-ad"></a>在 Azure AD 中建立服務主體 (Azure AD 應用程式)

1. 遵循這裡的指南來[註冊您的應用程式並設定權限](active-directory-interactive-connect-azure-sql-db.md#register-your-app-and-set-permissions)。

    務必新增**應用程式權限**及**委派的權限**。

    :::image type="content" source="media/authentication-aad-service-principals-tutorial/aad-apps.png" alt-text="aad-apps":::

    :::image type="content" source="media/authentication-aad-service-principals-tutorial/aad-app-registration-api-permissions.png" alt-text="api-permissions":::

2. 您也必須建立用於登入的用戶端密碼。 遵循這裡的指南來[上傳憑證或建立密碼以進行登入](../../active-directory/develop/howto-create-service-principal-portal.md#upload-a-certificate-or-create-a-secret-for-signing-in)。

3. 從您的應用程式註冊記錄下列各項。 您可以從 [概觀] 窗格中取得：
    - **應用程式識別碼**
    - **租用戶識別碼**：這應該會與之前的相同

在此教學課程中，我們將使用 *AppSP* 作為主要服務主體，並使用 *myapp* 作為第二個服務主體使用者 (將在 Azure SQL 中由 *AppSP* 所建立)。 您將需要建立兩個應用程式：*AppSP* 和 *myapp*。

如需如何建立 Azure AD 應用程式的詳細資訊，請參閱下文：[操作說明：使用入口網站來建立可存取資源的 Azure AD 應用程式和服務主體](../../active-directory/develop/howto-create-service-principal-portal.md)。


## <a name="create-the-service-principal-user-in-azure-sql-database"></a>在 Azure SQL Database 中建立服務主體使用者

在 Azure AD 中建立服務主體之後，在 SQL Database 中建立使用者。 您將必須使用具有權限的有效登入來連線到您的 SQL Database，以在資料庫中建立使用者。

1. 使用下列 T-SQL 命令，在 SQL Database 中建立使用者 *AppSP*：

    ```sql
    CREATE USER [AppSP] FROM EXTERNAL PROVIDER
    GO
    ```

2. 將 `db_owner` 權限授與 *AppSP*，讓使用者能夠在資料庫中建立其他 Azure AD 使用者。

    ```sql
    EXEC sp_addrolemember 'db_owner', [AppSP]
    GO
    ```

    如需詳細資訊，請參閱 [sp_addrolemember](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql) \(部分機器翻譯\)。

    或者，可以授與 `ALTER ANY USER` 權限，而不是提供 `db_owner` 角色。 這將可讓服務主體新增其他 Azure AD 使用者。

    ```sql
    GRANT ALTER ANY USER TO [AppSp]
    GO
    ```

    > [!NOTE]
    > 已針對伺服器將 *AppSP* 設定為 Azure AD 管理員時，就不需要上述設定。 若要針對 SQL 邏輯伺服器將服務主體設定為 AD 管理員，您可以使用 Azure 入口網站、PowerShell 或 Azure CLI 命令。 如需詳細資訊，請參閱[佈建 Azure AD 管理員 (SQL Database)](authentication-aad-configure.md?tabs=azure-powershell#powershell-for-sql-database-and-azure-synapse)。

## <a name="create-an-azure-ad-user-in-sql-database-using-an-azure-ad-service-principal"></a>使用 Azure AD 服務主體，在 SQL Database 中建立 Azure AD 使用者

> [!IMPORTANT]
> 用來登入 SQL Database 的服務主體必須具有用戶端密碼。 如果沒有，請遵循[在 Azure AD 中建立服務主體 (Azure AD 應用程式)](#create-a-service-principal-an-azure-ad-application-in-azure-ad) 的步驟 2。 在下列指令碼中，必須將此用戶端密碼新增為輸入參數。

1. 使用下列指令碼，使用服務主體 *AppSP* 來建立 Azure AD 服務主體使用者 *myapp*。

    - 使用稍早蒐集的 `TenantId` 取代 `<TenantId>`。
    - 使用稍早蒐集的 `ClientId` 取代 `<ClientId>`。
    - 使用稍早建立的用戶端密碼取代 `<ClientSecret>`。
    - 使用您自己的 SQL 邏輯伺服器名稱取代 `<server name>`。 如果您的伺服器名稱是 `myserver.database.windows.net`，使用 `myserver` 取代 `<server name>`。
    - 使用您的 SQL Database 名稱取代 `<database name>`。

    ```powershell
    # PowerShell script for creating a new SQL user called myapp using application AppSP with secret

    $tenantId = "<TenantId>"   #  tenantID (Azure Directory ID) were AppSP resides
    $clientId = "<ClientId>"   #  AppID also ClientID for AppSP     
    $clientSecret = "<ClientSecret>"   #  client secret for AppSP 
    $Resource = "https://database.windows.net/"
    
    $adalPath  = "${env:ProgramFiles}\WindowsPowerShell\Modules\AzureRM.profile\5.8.3"
    # To install the latest AzureRM.profile version execute  -Install-Module -Name AzureRM.profile
    $adal      = "$adalPath\Microsoft.IdentityModel.Clients.ActiveDirectory.dll"
    $adalforms = "$adalPath\Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll"
    [System.Reflection.Assembly]::LoadFrom($adal) | Out-Null
      $resourceAppIdURI = 'https://database.windows.net/'

      # Set Authority to Azure AD Tenant
      $authority = 'https://login.windows.net/' + $tenantId

      $ClientCred = [Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential]::new($clientId, $clientSecret)
      $authContext = [Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext]::new($authority)
      $authResult = $authContext.AcquireTokenAsync($resourceAppIdURI,$ClientCred)
      $Tok = $authResult.Result.CreateAuthorizationHeader()
      $Tok=$Tok.Replace("Bearer ","")
      Write-host "token"
      $Tok
      Write-host  " "

    $SQLServerName = "<server name>"    # Azure SQL logical server name 
    Write-Host "Create SQL connectionstring"
    $conn = New-Object System.Data.SqlClient.SQLConnection 
    $DatabaseName = "<database name>"     # Azure SQL database name
    $conn.ConnectionString = "Data Source=$SQLServerName.database.windows.net;Initial Catalog=$DatabaseName;Connect Timeout=30"
    $conn.AccessToken = $Tok
    
    Write-host "Connect to database and execute SQL script"
    $conn.Open() 
    $ddlstmt = 'CREATE USER [myapp] FROM EXTERNAL PROVIDER;'
    Write-host " "
    Write-host "SQL DDL command"
    $ddlstmt
    $command = New-Object -TypeName System.Data.SqlClient.SqlCommand($ddlstmt, $conn)       
    
    Write-host "results"
    $command.ExecuteNonQuery()
    $conn.Close()  
    ``` 

    或者，您可以使用 [Azure AD 服務主體驗證至 SQL DB - 程式碼範例](https://techcommunity.microsoft.com/t5/azure-sql-database/azure-ad-service-principal-authentication-to-sql-db-code-sample/ba-p/481467) \(英文\) 部落格中的程式碼範例。 修改指令碼，以執行 DDL 陳述式 `CREATE USER [myapp] FROM EXTERNAL PROVIDER`。 您可以使用相同的指令碼，在 SQL Database 中建立一般的 Azure AD 使用者群組。

    > [!NOTE]
    > 如果您需要安裝 AzureRM.profile 模組，則必須以管理員身分開啟 PowerShell。 您可以使用下列命令，自動安裝最新的 AzureRM.profile 版本，並針對上述指令碼設定 `$adalpath`：
    > 
    > ```powershell
    > Install-Module AzureRM.profile -force
    > Import-Module AzureRM.profile
    > $version = (Get-Module -Name AzureRM.profile).Version.toString()
    > $adalPath = "${env:ProgramFiles}\WindowsPowerShell\Modules\AzureRM.profile\${version}"
    > ```
    
2. 執行下列命令，以檢查使用者 *myapp* 是否存在於資料庫中：

    ```sql
    SELECT name, type, type_desc, CAST(CAST(sid as varbinary(16)) as uniqueidentifier) as appId from sys.database_principals WHERE name = 'myapp'
    GO
    ```

    您應該會看到如下的輸出：

    ```output
    name    type    type_desc   appId
    myapp   E   EXTERNAL_USER   6d228f48-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    ```

## <a name="next-steps"></a>後續步驟

- [使用 Azure SQL 的 Azure Active Directory 服務主體](authentication-aad-service-principal.md)
- [什麼是 Azure 資源受控識別？](../../active-directory/managed-identities-azure-resources/overview.md)
- [如何使用 App Service 和 Azure Functions 的受控身分識別](../../app-service/overview-managed-identity.md)
- [Azure AD 服務主體驗證至 SQL DB - 程式碼範例](https://techcommunity.microsoft.com/t5/azure-sql-database/azure-ad-service-principal-authentication-to-sql-db-code-sample/ba-p/481467) \(英文\)
- [Azure Active Directory 中的應用程式和服務主體物件](../../active-directory/develop/app-objects-and-service-principals.md)
- [使用 Azure PowerShell 來建立 Azure 服務主體](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps)