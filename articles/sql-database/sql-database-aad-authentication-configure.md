---
title: 設定 Azure Active Directory 驗證
description: 在設定 Azure AD 之後，瞭解如何使用 Azure Active Directory 驗證來連線到 SQL Database、受控實例和 Azure Synapse 分析。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: azure-synapse
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto, carlrab
ms.date: 03/27/2020
ms.openlocfilehash: 0e244ea185011bbb7d9f0facad399bb9b577bbc2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80419868"
---
# <a name="configure-and-manage-azure-active-directory-authentication-with-sql"></a>使用 SQL 設定及管理 Azure Active Directory 驗證

本文說明如何建立和填入 Azure AD，然後使用 Azure AD 搭配 Azure [SQL Database （SQL DB）](sql-database-technical-overview.md)、[受控實例（MI）](sql-database-managed-instance.md)和[azure Synapse Analytics （先前為 Azure SQL 資料倉儲）](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)。 如需概觀，請參閱 [Azure Active Directory 驗證](sql-database-aad-authentication.md)。

> [!NOTE]
> 本文適用于 Azure SQL server，以及 SQL Database 和 Azure Synapse。 為了簡單起見，在同時參考 SQL Database 和 Azure Synapse 時，會使用 SQL Database。

> [!IMPORTANT]  
> 使用 Azure Active Directory 帳戶不支援連線到 Azure VM 上執行的 SQL Server。 請改用 Active Directory 網域帳戶。

## <a name="azure-ad-authentication-methods"></a>Azure AD 驗證方法

Azure AD authentication 支援下列驗證方法：

- Azure AD 僅限雲端的身分識別
- Azure AD 支援的混合式身分識別：
  - 使用兩個選項結合無縫單一登入（SSO）的雲端驗證
    - Azure AD 密碼雜湊驗證
    - Azure AD 傳遞驗證
  - 同盟驗證

如需 Azure AD 驗證方法，以及要選擇哪一項的詳細資訊，請參閱下列文章：
- [針對 Azure Active Directory 混合式身分識別解決方案選擇正確的驗證方法](../active-directory/hybrid/choose-ad-authn.md)

如需有關 Azure AD 混合式身分識別、設定和同步處理的詳細資訊，請參閱下列文章：

- 密碼雜湊驗證-[使用 Azure AD Connect 同步來執行密碼雜湊同步處理](../active-directory/hybrid/how-to-connect-password-hash-synchronization.md)
- 傳遞驗證- [Azure Active Directory 傳遞驗證](../active-directory/hybrid/how-to-connect-pta-quick-start.md)
- 同盟驗證-[在 Azure 中部署 Active Directory 同盟服務](/windows-server/identity/ad-fs/deployment/how-to-connect-fed-azure-adfs)，以及[Azure AD Connect 和同盟](../active-directory/hybrid/how-to-connect-fed-whatis.md)

SQL DB （單一資料庫和資料庫集區）、受控實例和 Azure Synapse 都支援上述所有驗證方法。

## <a name="create-and-populate-an-azure-ad"></a>建立和填入 Azure AD

建立 Azure AD 並利用使用者和群組填入。 Azure AD 可以是初始 Azure AD 受控網域。 Azure AD 也可以是與 Azure AD 同盟的內部部署 Active Directory 網域服務。

如需詳細資訊，請參閱[整合內部部署身分識別與 Azure Active Directory](../active-directory/hybrid/whatis-hybrid-identity.md)、[將您自己的網域名稱新增至 Azure AD](../active-directory/active-directory-domains-add-azure-portal.md)、[Microsoft Azure 現在支援與 Windows Server Active Directory 同盟](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/)、[管理您的 Azure AD 目錄](../active-directory/fundamentals/active-directory-administer.md)、[使用 Windows PowerShell 管理 Azure AD](/powershell/azure/overview) 和[混合式身分識別所需的連接埠和通訊協定](../active-directory/hybrid/reference-connect-ports.md)。

## <a name="associate-or-add-an-azure-subscription-to-azure-active-directory"></a>將 Azure 訂用帳戶關聯或新增至 Azure Active Directory

1. 讓目錄成為裝載資料庫之 Azure 訂用帳戶信任的目錄，以將 Azure 訂用帳戶與 Azure Active Directory 建立關聯。 如需詳細資料，請參閱 [Azure 訂用帳戶如何與 Azure Active Directory 建立關聯](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)。

2. 使用 Azure 入口網站中的目錄切換器，切換至與網域建立關聯的訂用帳戶。

   > [!IMPORTANT]
   > 每個 Azure 訂用帳戶都會與 Azure AD 執行個體有信任關係。 這表示它信任該目錄來驗證使用者、服務和裝置。 多個訂用帳戶可以信任相同的目錄，但是一個訂用帳戶只能信任一個目錄。 這個訂用帳戶與目錄之間存在的信任關係不同於訂用帳戶與所有其他 Azure 資源 (網站、資料庫等) 之間的關係，後者比較像是訂用帳戶的子資源。 如果訂用帳戶已過期，則也會停止存取與該訂用帳戶相關聯的其他資源。 但目錄會保留在 Azure 中，而且您可以將其他訂用帳戶與該目錄產生關聯，並繼續管理目錄使用者。 如需有關資源的詳細資訊，請參閱 [了解 Azure 中的資源存取](../active-directory/active-directory-b2b-admin-add-users.md)。 若要深入了解此信任關聯性，請參閱[如何將 Azure 訂用帳戶關聯或新增至 Azure Active Directory](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)。

## <a name="create-an-azure-ad-administrator-for-azure-sql-server"></a>建立 Azure SQL 伺服器的 Azure AD 系統管理員

每個 Azure SQL 伺服器（裝載 SQL Database 或 Azure Synapse）都是以單一伺服器系統管理員帳戶開始，這是整個 Azure SQL server 的系統管理員。 第二個 SQL Server 系統管理員必須建立，也就是 Azure AD 帳戶。 這個主體會在 master 資料庫中建立為自主資料庫使用者。 身為系統管理員，伺服器系統管理員帳戶是每個使用者資料庫中的 **db_owner** 角色成員，並且會進入每個使用者資料庫做為 **dbo** 使用者。 如需有關伺服器管理員帳戶的詳細資訊，請參閱[管理 Azure SQL Database 的資料庫和登入](sql-database-manage-logins.md)。

將 Azure Active Directory 與異地複寫搭配使用時，必須為主要和次要伺服器設定 Azure Active Directory 系統管理員。 如果伺服器沒有 Azure Active Directory 系統管理員，則 Azure Active Directory 登入和使用者將會收到「無法連線至伺服器」錯誤。

> [!NOTE]
> 使用者如果不是以 Azure AD 帳戶 (包括 Azure SQL 伺服器系統管理員帳戶) 為基礎，就無法建立以 Azure AD 為基礎的使用者，因為他們不具備向 Azure AD 驗證建議之資料庫使用者的權限。

## <a name="provision-an-azure-active-directory-administrator-for-your-managed-instance"></a>為受控實例布建 Azure Active Directory 系統管理員

> [!IMPORTANT]
> 如果您要布建受控實例，請只遵循下列步驟。 這項作業只能由全域/公司管理員或 Azure AD 中的特殊許可權角色管理員執行。 下列步驟說明為目錄中具有不同權限的使用者授與權限的程序。

> [!NOTE]
> 針對在 GA 之前建立的 MI Azure AD 管理員，但繼續作業 GA 之後，現有的行為不會有任何功能變更。 如需詳細資訊，請參閱[MI 的新 Azure AD 系統管理員功能](#new-azure-ad-admin-functionality-for-mi)一節以取得詳細資料。

您的受控實例需要許可權才能讀取 Azure AD，以順利完成工作，例如透過安全性群組成員資格或建立新使用者來驗證使用者。 若要這麼做，您必須將許可權授與受控實例，以讀取 Azure AD。 執行方式有兩種：從入口網站和 PowerShell。 以下將逐步說明這兩種方法。

1. 在 Azure 入口網站的右上角，選取您的連線以顯示可能的 Active Directory 下拉式清單。

2. 選擇正確的 Active Directory 做為預設 Azure AD。

   此步驟會將 Active Directory 的相關訂用帳戶與「受控執行個體」連結，確定 Azure AD 與「受控執行個體」使用相同的訂用帳戶。

3. 瀏覽至「受控執行個體」，然後選取要用於 Azure AD 整合的執行個體。

   ![aad](./media/sql-database-aad-authentication/aad.png)

4. 選取 [Active Directory 管理員] 頁面頂端的橫幅，並對目前的使用者授與權限。 如果您是在 Azure AD 中以全域/公司管理員的身分登入，您可以從 Azure 入口網站或使用 PowerShell 搭配下列腳本來執行此動作。

    ![授與權限 - 入口網站](./media/sql-database-aad-authentication/grant-permissions.png)

    ```powershell
    # Gives Azure Active Directory read permission to a Service Principal representing the managed instance.
    # Can be executed only by a "Company Administrator", "Global Administrator", or "Privileged Role Administrator" type of user.

    $aadTenant = "<YourTenantId>" # Enter your tenant ID
    $managedInstanceName = "MyManagedInstance"

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
    $roleMember = Get-AzureADServicePrincipal -SearchString $managedInstanceName
    $roleMember.Count
    if ($roleMember -eq $null) {
        Write-Output "Error: No Service Principals with name '$    ($managedInstanceName)', make sure that managedInstanceName parameter was     entered correctly."
        exit
    }
    if (-not ($roleMember.Count -eq 1)) {
        Write-Output "Error: More than one service principal with name pattern '$    ($managedInstanceName)'"
        Write-Output "Dumping selected service principals...."
        $roleMember
        exit
    }

    # Check if service principal is already member of readers role
    $allDirReaders = Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
    $selDirReader = $allDirReaders | where{$_.ObjectId -match     $roleMember.ObjectId}

    if ($selDirReader -eq $null) {
        # Add principal to readers role
        Write-Output "Adding service principal '$($managedInstanceName)' to     'Directory Readers' role'..."
        Add-AzureADDirectoryRoleMember -ObjectId $role.ObjectId -RefObjectId     $roleMember.ObjectId
        Write-Output "'$($managedInstanceName)' service principal added to     'Directory Readers' role'..."

        #Write-Output "Dumping service principal '$($managedInstanceName)':"
        #$allDirReaders = Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
        #$allDirReaders | where{$_.ObjectId -match $roleMember.ObjectId}
    }
    else {
        Write-Output "Service principal '$($managedInstanceName)' is already     member of 'Directory Readers' role'."
    }
    ```

5. 順利完成作業之後，右上角就會出現下列通知：

    ![成功](./media/sql-database-aad-authentication/success.png)

6. 現在您可以為受控實例選擇您的 Azure AD 系統管理員。 若要這麼做，請在 [Active Directory 系統管理員] 頁面上，選取 [設定系統管理員]**** 命令。

    ![設定系統管理員](./media/sql-database-aad-authentication/set-admin.png)

7. 在 [AAD 系統管理員] 頁面中，搜尋使用者，選取要成為系統管理員的使用者或群組，然後選取 [選取]****。

   [Active Directory 系統管理員] 頁面會顯示您 Active Directory 的所有成員和群組。 呈現灰色的使用者或群組無法選取，因為他們不受支援成為 Azure AD 系統管理員。 請參閱 [Azure AD 功能和限制](sql-database-aad-authentication.md#azure-ad-features-and-limitations) 中支援的系統管理員清單。 角色型存取控制 (RBAC) 僅適用於 Azure 入口網站，不會傳播至 SQL Server。

    ![新增 Azure Active Directory 系統管理員](./media/sql-database-aad-authentication/add-azure-active-directory-admin.png)

8. 在 [Active Directory 管理員] 頁面頂端，選取 [儲存]****。

    ![儲存](./media/sql-database-aad-authentication/save.png)

    變更系統管理員的程序可能需要幾分鐘的時間。 接著，新的系統管理員就會出現在 [Active Directory 系統管理員] 方塊中。

為受控實例布建 Azure AD 系統管理員之後，您就可以開始使用<a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">CREATE LOGIN</a>語法來建立 Azure AD 伺服器主體（登入）。 如需詳細資訊，請參閱[受控實例總覽](sql-database-managed-instance.md#azure-active-directory-integration)。

> [!TIP]
> 若稍後要移除系統管理員，請在 [Active Directory 系統管理員] 頁面頂端，選取 [移除系統管理員]****，然後選取 [儲存]****。

### <a name="new-azure-ad-admin-functionality-for-mi"></a>MI 的新 Azure AD 系統管理員功能

下表摘要說明適用于 MI 的公用預覽 Azure AD 登入系統管理員的功能，與 Azure AD 登入的 GA 所提供的新功能。

| 在公開預覽期間，針對 MI Azure AD 登入管理員 | 適用于 MI Azure AD 系統管理員的 GA 功能 |
| --- | ---|
| 的運作方式類似于 SQL Database 的 Azure AD 系統管理員，這會啟用 Azure AD 驗證，但 Azure AD 系統管理員無法在主要 db 中為 MI 建立 Azure AD 或 SQL 登入。 | Azure AD admin 具有 sysadmin 許可權，而且可以在 master db 中為 MI 建立 AAD 和 SQL 登入。 |
| 不存在於 sys.databases server_principals 視圖中 | 會出現在 sys.databases server_principals 視圖中 |
| 允許將個別 Azure AD 來賓使用者設定為 MI 的 Azure AD 系統管理員。 如需詳細資訊，請參閱[在 Azure 入口網站中新增 AZURE ACTIVE DIRECTORY B2B](../active-directory/b2b/add-users-administrator.md)共同作業使用者。 | 需要建立包含來賓使用者的 Azure AD 群組，做為 MI 的 Azure AD 管理員，將此群組設定為成員。 如需詳細資訊，請參閱[Azure AD 企業對企業支援](sql-database-ssms-mfa-authentication.md#azure-ad-business-to-business-support)。 |

最佳做法是針對 GA 之前建立的 MI Azure AD 系統管理員，但仍在公開發行後，針對相同的 Azure AD 使用者或群組，使用 Azure 入口網站的 [移除系統管理員] 和 [設定系統管理員] 選項來重設 Azure AD 系統管理員。

### <a name="known-issues-with-the-azure-ad-login-ga-for-mi"></a>適用于 MI 的 Azure AD 登入 GA 的已知問題

- 如果在用於 MI 的 master 資料庫中存在 Azure AD 登入（使用 T-sql 命令`CREATE LOGIN [myaadaccount] FROM EXTERNAL PROVIDER`建立），則無法將其設定為 mi 的 Azure AD 系統管理員。 當您使用 Azure 入口網站、PowerShell 或 CLI 命令建立 Azure AD 登入，將登入設定為 Azure AD 管理員時，將會遇到錯誤。
  - 您必須先使用命令`DROP LOGIN [myaadaccount]`將登入放在 master 資料庫中，才能將帳戶建立為 Azure AD 系統管理員。
  - `DROP LOGIN`成功之後，在 Azure 入口網站中設定 Azure AD 系統管理員帳戶。 
  - 如果您無法設定 Azure AD 系統管理員帳戶，請在受控實例的 master 資料庫中查看登入。 使用下列命令：`SELECT * FROM sys.server_principals`
  - 設定 MI 的 Azure AD 系統管理員，將會在此帳戶的 master 資料庫中自動建立登入。 移除 Azure AD 系統管理員，將會自動從 master 資料庫中卸載登入。

- 不支援個別 Azure AD 來賓使用者作為 MI Azure AD 系統管理員。 來賓使用者必須是要設定為 Azure AD 管理員的 Azure AD 群組的一部分。目前，[Azure 入口網站] 分頁不會針對另一個 Azure AD 呈現灰色的來賓使用者，讓使用者可以繼續進行系統管理員設定。 將來賓使用者儲存為 Azure AD 系統管理員會導致安裝程式失敗。
  - 如果您想要讓來賓使用者成為 MI 的 Azure AD 系統管理員，請在 Azure AD 群組中包含來賓使用者，並將此群組設定為 Azure AD 系統管理員。

### <a name="powershell-for-sql-managed-instance"></a>適用于 SQL 受控實例的 PowerShell

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

若要執行 PowerShell Cmdlet，Azure PowerShell 必須已安裝且正在執行中。 如需詳細資訊，請參閱 [如何安裝和設定 Azure PowerShell](/powershell/azure/overview)。

> [!IMPORTANT]
> Azure SQL Database 仍然支援 PowerShell Azure Resource Manager （RM）模組，但所有未來的開發都是針對 Az .Sql 模組。 AzureRM 模組會繼續收到錯誤修正，直到2020年12月為止。  Az 模組和 AzureRm 模組中命令的引數本質上完全相同。 如需其相容性的詳細資訊，請參閱[新的 Azure PowerShell Az 模組簡介](/powershell/azure/new-azureps-module-az)。

若要佈建 Azure AD 系統管理員，請執行下列 Azure PowerShell 命令：

- Connect-AzAccount
- 選取-Get-azsubscription

用來布建和管理 SQL 受控實例之 Azure AD 管理員的 Cmdlet：

| Cmdlet 名稱 | 描述 |
| --- | --- |
| [設定-AzSqlInstanceActiveDirectoryAdministrator](/powershell/module/az.sql/set-azsqlinstanceactivedirectoryadministrator) |為目前訂用帳戶中的 SQL 受控實例布建 Azure AD 系統管理員。 （必須來自目前的訂用帳戶）|
| [移除-AzSqlInstanceActiveDirectoryAdministrator](/powershell/module/az.sql/remove-azsqlinstanceactivedirectoryadministrator) |移除目前訂用帳戶中 SQL 受控實例的 Azure AD 系統管理員。 |
| [AzSqlInstanceActiveDirectoryAdministrator](/powershell/module/az.sql/get-azsqlinstanceactivedirectoryadministrator) |傳回目前訂用帳戶中 SQL 受控實例之 Azure AD 系統管理員的相關資訊。|

下列命令會針對名為 ManagedInstance01 的受控實例，取得與名為 ResourceGroup01 的資源群組相關聯的 Azure AD 系統管理員的資訊。

```powershell
Get-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstance01"
```

下列命令會針對名為 ManagedInstance01 的受控實例，布建名為 Dba 的 Azure AD 系統管理員群組。 此伺服器與資源群組 ResourceGroup01 相關聯。

```powershell
Set-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstance01" -DisplayName "DBAs" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353b"
```

下列命令會移除名為 ManagedInstanceName01 且與資源群組 ResourceGroup01 相關聯之受控實例的 Azure AD 系統管理員。

```powershell
Remove-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstanceName01" -Confirm -PassThru
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

您也可以呼叫下列 CLI 命令，為 SQL 受控實例布建 Azure AD 管理員：

| Command | 描述 |
| --- | --- |
|[az sql mi ad-admin create](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-create) | 為 SQL 受控實例布建 Azure Active Directory 系統管理員。 （必須來自目前的訂用帳戶） |
|[az sql mi ad-管理員刪除](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-delete) | 移除 SQL 受控實例的 Azure Active Directory 系統管理員。 |
|[az sql mi ad-admin list](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-list) | 傳回目前針對 SQL 受控實例所設定之 Azure Active Directory 系統管理員的相關資訊。 |
|[az sql mi ad-管理更新](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-update) | 更新 SQL 受控實例的 Active Directory 系統管理員。 |

如需 CLI 命令的詳細資訊，請參閱[az sql mi](/cli/azure/sql/mi)。

* * *

## <a name="provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server"></a>為 Azure SQL Database 伺服器佈建 Azure Active Directory 系統管理員

> [!IMPORTANT]
> 如果您要布建 Azure SQL Database 伺服器或 Azure Synapse 分析，請只依照下列步驟執行。

下列兩個程序會示範如何在 Azure 入口網站以及使用 PowerShell，佈建 Azure SQL 伺服器的 Azure Active Directory 系統管理員。

### <a name="azure-portal"></a>Azure 入口網站

1. 在 [Azure 入口網站](https://portal.azure.com/)的右上角，選取您的連線以顯示可能的 Active Directory 下拉式清單。 選擇正確的 Active Directory 做為預設 Azure AD。 此步驟會連結與訂用帳戶相關聯的 Active Directory 和 Azure SQL 伺服器，確定 Azure AD 和 SQL Server 使用相同的訂用帳戶。 （Azure SQL server 可裝載 Azure SQL Database 或 Azure Synapse）。

    ![choose-ad][8]

2. 搜尋並選取 **[SQL server**]。

    ![搜尋並選取 [SQL server]](media/sql-database-aad-authentication/search-for-and-select-sql-servers.png)

    >[!NOTE]
    > 在此頁面上，於選取 [SQL Server]**** 之前，您可以選取名稱旁的**星星**將該類別設為「我的最愛」**，並將 [SQL Server]**** 新增至左側的導覽列。

3. 在 [SQL Server]**** 頁面上，選取 [Active Directory 系統管理員]****。

4. 在 [Active Directory 系統管理員]**** 頁面中，選取 [設定系統管理員]****。

    ![Active Directory 管理員設定的 SQL server](./media/sql-database-aad-authentication/sql-servers-set-active-directory-admin.png)  

5. 在 [**新增系統管理員**] 頁面中，搜尋使用者，選取要成為系統管理員的使用者或群組，然後選取 [**選取**]。 [Active Directory 系統管理員] 頁面會顯示您 Active Directory 的所有成員和群組。 呈現灰色的使用者或群組無法選取，因為他們不受支援成為 Azure AD 系統管理員。 （請參閱**Azure AD 功能和限制**一節中的支援的系統管理員清單，以[SQL Database 或 Azure Synapse 驗證使用 Azure Active Directory 驗證](sql-database-aad-authentication.md)。）角色型存取控制（RBAC）僅適用于入口網站，不會傳播至 SQL Server。

    ![選取 Azure Active Directory 系統管理員](./media/sql-database-aad-authentication/select-azure-active-directory-admin.png)  

6. 在**Active Directory 管理**] 頁面的頂端，選取 [**儲存**]。

    ![儲存系統管理員](./media/sql-database-aad-authentication/save-admin.png)

變更系統管理員的程序可能需要幾分鐘的時間。 接著，新的系統管理員就會出現在 [Active Directory 系統管理員] **** 方塊中。

   > [!NOTE]
   > 設定 Azure AD 系統管理員時，新的系統管理員名稱 (使用者或群組) 不可以已經存在於虛擬主要資料庫中作為 SQL Server 驗證使用者。 如果存在，Azure AD 系統管理員設定將會失敗；其中會復原其建立並指出這樣的系統管理員 (名稱) 已經存在。 由於這類 SQL Server 驗證使用者並非 Azure AD 的成員，因此使用 Azure AD 驗證來連線到伺服器的一切努力都會失敗。

若稍後要移除系統管理員，請在 [Active Directory 系統管理員]**** 頁面頂端，選取 [移除系統管理員]****，然後選取 [儲存]****。

### <a name="powershell-for-azure-sql-database-and-azure-synapse"></a>適用于 Azure SQL Database 和 Azure Synapse 的 PowerShell

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

若要執行 PowerShell Cmdlet，Azure PowerShell 必須已安裝且正在執行中。 如需詳細資訊，請參閱 [如何安裝和設定 Azure PowerShell](/powershell/azure/overview)。 若要佈建 Azure AD 系統管理員，請執行下列 Azure PowerShell 命令：

- Connect-AzAccount
- 選取-Get-azsubscription

用來布建和管理 Azure SQL Database 和 Azure Synapse 之 Azure AD 系統管理員的 Cmdlet：

| Cmdlet 名稱 | 描述 |
| --- | --- |
| [設定-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/set-azsqlserveractivedirectoryadministrator) |布建 Azure SQL server 或 Azure Synapse 的 Azure Active Directory 系統管理員。 （必須來自目前的訂用帳戶） |
| [移除-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/remove-azsqlserveractivedirectoryadministrator) |移除 Azure SQL server 或 Azure Synapse 的 Azure Active Directory 系統管理員。 |
| [AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/get-azsqlserveractivedirectoryadministrator) |傳回目前為 Azure SQL server 或 Azure Synapse 設定的 Azure Active Directory 系統管理員的相關資訊。 |

使用 PowerShell 命令 get-help 來查看每個命令的詳細資訊。 例如： `get-help Set-AzSqlServerActiveDirectoryAdministrator` 。

下列指令碼會在名為 **Group-23** 的資源群組中，為 **demo_server** 伺服器佈建名為 **DBA_Group** (物件識別碼 `40b79501-b343-44ed-9ce7-da4c8cc7353f`) 的 Azure AD 系統管理員群組：

```powershell
Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" -DisplayName "DBA_Group"
```

**DisplayName** 輸入參數可接受 Azure AD 顯示名稱或「使用者主體名稱」。 例如 ``DisplayName="John Smith"`` 和 ``DisplayName="johns@contoso.com"``。 Azure AD 群組只支援 Azure AD 顯示名稱。

> [!NOTE]
> Azure PowerShell 命令 ```Set-AzSqlServerActiveDirectoryAdministrator``` 不會阻止您為不支援的使用者佈建 Azure AD 系統管理員。 您可以佈建不支援的使用者，但是該使用者無法連線到資料庫。

下列範例使用選用的 **ObjectID**：

```powershell
Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" `
    -DisplayName "DBA_Group" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353f"
```

> [!NOTE]
> 當 **DisplayName** 並非唯一時，就需要 Azure AD **ObjectID**。 若要擷取 **ObjectID** 和 **DisplayName** 的值，請使用 Azure 傳統入口網站的 [Active Directory] 區段，然後檢視使用者或群組的屬性。

下列範例會傳回 Azure SQL 伺服器的目前 Azure AD 系統管理員的相關資訊：

```powershell
Get-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" | Format-List
```

下列範例會移除 Azure AD 系統管理員：

```powershell
Remove-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

您可以藉由呼叫下列 CLI 命令來布建 Azure AD 管理員：

| Command | 描述 |
| --- | --- |
|[az sql server ad-admin create](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-create) | 布建 Azure SQL server 或 Azure Synapse 的 Azure Active Directory 系統管理員。 （必須來自目前的訂用帳戶） |
|[az sql server ad-admin delete](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-delete) | 移除 Azure SQL server 或 Azure Synapse 的 Azure Active Directory 系統管理員。 |
|[az sql server ad-admin list](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-list) | 傳回目前為 Azure SQL server 或 Azure Synapse 設定的 Azure Active Directory 系統管理員的相關資訊。 |
|[az sql server ad-admin update](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-update) | 更新 Azure SQL server 或 Azure Synapse 的 Active Directory 系統管理員。 |

如需 CLI 命令的詳細資訊，請參閱[az sql server](/cli/azure/sql/server)。

* * *

> [!NOTE]
> 您也可以使用 REST API 來佈建 Azure Active Directory 系統管理員。 如需詳細資訊，請參閱 [Azure SQL Database 之 Azure SQL Database 作業的 Service Management REST API 參考和作業](/rest/api/sql/)

## <a name="configure-your-client-computers"></a>設定用戶端電腦

在所有用戶端電腦上，您的應用程式或使用者會使用 Azure AD 身分識別連線到 Azure SQL Database 或 Azure Synapse，您必須安裝下列軟體：

- 從[https://msdn.microsoft.com/library/5a4x27ek.aspx](https://msdn.microsoft.com/library/5a4x27ek.aspx).NET Framework 4.6 或更新版本。
- SQL Server 的 Azure Active Directory 驗證程式庫（*ADAL）。DLL*）。 以下是安裝最新的 SSMS、ODBC 和包含 ADAL 之 OLE DB 驅動程式的下載連結 *。DLL 連結*庫。
    1. [Transact-SQL](/sql/ssms/download-sql-server-management-studio-ssms)
    1. [ODBC Driver 17 for SQL Server](https://www.microsoft.com/download/details.aspx?id=56567)
    1. [SQL Server 的 OLE DB Driver 18](https://www.microsoft.com/download/details.aspx?id=56730)

您可以符合這些需求，方法如下︰

- 安裝最新版本的[SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)或[SQL Server Data Tools](/sql/ssdt/download-sql-server-data-tools-ssdt)符合 .NET Framework 4.6 需求。
    - SSMS 會安裝 x86 版本的*ADAL。DLL*。
    - SSDT 會安裝 ADAL 的 amd64 版本 *。DLL*。
    - [Visual Studio 下載](https://www.visualstudio.com/downloads/download-visual-studio-vs)的最新 Visual Studio 符合 .NET Framework 4.6 需求，但不會安裝所需的 Amd64 版本*ADAL。DLL*。

## <a name="create-contained-database-users-in-your-database-mapped-to-azure-ad-identities"></a>在對應至 Azure AD 身分識別的資料庫中建立自主資料庫使用者

> [!IMPORTANT]
> 受控實例現在支援 Azure AD 伺服器主體（登入），可讓您從 Azure AD 的使用者、群組或應用程式建立登入。 Azure AD 伺服器主體（登入）可讓您對受控實例進行驗證，而不需要將資料庫使用者建立為自主資料庫使用者。 如需詳細資訊，請參閱[受控實例總覽](sql-database-managed-instance.md#azure-active-directory-integration)。 如需建立 Azure AD 伺服器主體 (登入) 的語法，請參閱 <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">CREATE LOGIN</a>。

Azure Active Directory 驗證需要建立資料庫使用者做為自主資料庫使用者。 以 Azure AD 身分識別為基礎的自主資料庫使用者係指在 master 資料庫中沒有登入身分的資料庫使用者，並且此使用者會對應至 Azure AD 目錄中與資料庫關聯的身分識別。 Azure AD 身分識別可以是個別的使用者帳戶或群組。 如需有關自主資料庫使用者的詳細資訊，請參閱 [自主資料庫使用者 - 使資料庫可攜](https://msdn.microsoft.com/library/ff929188.aspx)。

> [!NOTE]
> 您無法使用 Azure 入口網站建立資料庫使用者 (系統管理員除外)。 RBAC 角色不會傳播至 SQL Server、SQL Database 或 Azure Synapse。 Azure RBAC 角色可用來管理 Azure 資源，並不會套用到資料庫權限。 例如，「 **SQL Server 參與者**」角色不會授與存取權以連線到 SQL Database 或 Azure Synapse。 存取權限必須使用 Transact-SQL 陳述式直接在資料庫中授與。

> [!WARNING]
> 不支援在 T-SQL CREATE LOGIN 和 CREATE USER 陳述式中的使用者名稱內使用冒號 `:` 或 `&` 等特殊字元。

若要建立以 Azure AD 為基礎的自主資料庫使用者 (而非擁有資料庫的伺服器系統管理員)，請以至少具有 **ALTER ANY USER** 權限的使用者身分，使用 Azure AD 身分識別來連線到資料庫。 然後使用下列的 Transact-SQL 語法：

```sql
CREATE USER <Azure_AD_principal_name> FROM EXTERNAL PROVIDER;
```

Azure_AD_principal_name** 可以是 Azure AD 使用者的使用者主體名稱或 Azure AD 群組的顯示名稱。

**範例：** 建立代表 Azure AD 同盟或受控網域使用者的自主資料庫使用者：

```sql
CREATE USER [bob@contoso.com] FROM EXTERNAL PROVIDER;
CREATE USER [alice@fabrikam.onmicrosoft.com] FROM EXTERNAL PROVIDER;
```

若要建立代表 Azure AD 或同盟網域群組的自主資料庫使用者，請輸入安全性群組的顯示名稱：

```sql
CREATE USER [ICU Nurses] FROM EXTERNAL PROVIDER;
```

若要建立代表使用 Azure AD 權杖進行連線之應用程式的自主資料庫使用者︰

```sql
CREATE USER [appName] FROM EXTERNAL PROVIDER;
```

> [!NOTE]
> 此命令需要代表已登入使用者的 SQL 存取 Azure AD （「外部提供者」）。 有時候，會導致 Azure AD 將例外狀況傳回給 SQL 的情況發生。 在這些情況下，使用者會看到 SQL 錯誤33134，其中應該包含 AAD 特定的錯誤訊息。 在大部分的情況下，此錯誤會指出存取遭到拒絕，或使用者必須註冊 MFA 才能存取資源，或是必須透過 preauthorization 來處理第一方應用程式之間的存取權。 在前兩個案例中，此問題通常是由使用者的 AAD 租使用者中設定的條件式存取原則所造成：它們會讓使用者無法存取外部提供者。 更新 CA 原則以允許存取應用程式 ' 00000002-0000-0000-c000-000000000000 ' （AAD 圖形 API 的應用程式識別碼）應可解決此問題。 如果錯誤指出第一方應用程式之間的存取必須透過 preauthorization 處理，問題是因為使用者已以服務主體的身分登入。 如果是由使用者執行，則命令應會成功。

> [!TIP]
> 您無法從 Azure Active Directory 直接建立使用者，除了與您的 Azure 訂用帳戶相關聯的 Azure Active Directory 以外。 不過，在相關聯 Active Directory (稱為外部使用者) 中匯入之使用者的其他 Active Directory 成員可以新增至租用戶 Active Directory 中的 Active Directory 群組。 藉由建立該 AD 群組的自主資料庫使用者，來自外部 Active Directory 的使用者可以存取 SQL Database。

如需有關根據 Azure Active Directory 身分識別建立自主資料庫使用者的詳細資訊，請參閱 [CREATE USER (Transact-SQL)](https://msdn.microsoft.com/library/ms173463.aspx)。

> [!NOTE]
> 移除 Azure SQL 伺服器的 Azure Active Directory 系統管理員可防止任何 Azure AD 驗證使用者連線到伺服器。 必要時，SQL Database 系統管理員可以手動刪除無法使用的 Azure AD 使用者。

> [!NOTE]
> 如果您收到**連線逾時過期**，您可能需要將 `TransparentNetworkIPResolution` 連接字串的參數設定為 false。 如需詳細資訊，請參閱 [.NET Framework 4.6.1 的連線逾時問題 - TransparentNetworkIPResolution](https://blogs.msdn.microsoft.com/dataaccesstechnologies/20../../connection-timeout-issue-with-net-framework-4-6-1-transparentnetworkipresolution/)。

當您建立資料庫使用者時，該使用者會獲得 **CONNECT** 權限，而可以用 **PUBLIC** 角色的成員身分連線到該資料庫。 一開始提供給使用者的權限僅限於已授與 **PUBLIC** 角色的任何權限，或已授與其所屬任何 Azure AD 群組的任何權限。 一旦您佈建以 Azure AD 為基礎的自主資料庫使用者，您可以使用您授與權限給任何其他類型使用者的相同方式，授與該使用者額外的權限。 通常會授與權限給資料庫角色並新增使用者至角色。 如需詳細資訊，請參閱 [Database Engine 權限基本概念](https://social.technet.microsoft.com/wiki/contents/articles/4433.database-engine-permission-basics.aspx)。 如需有關特殊 SQL Database 角色的詳細資訊，請參閱 [管理 Azure SQL Database 的資料庫和登入](sql-database-manage-logins.md)。
以外部使用者身分匯入至管理網域的同盟網域使用者帳戶必須使用受控網域身分識別。

> [!NOTE]
> Azure AD 使用者會在資料庫中繼資料中標示為類型 E (EXTERNAL_USER)，而群組則標示為類型 X (EXTERNAL_GROUPS)。 如需詳細資訊，請參閱 [sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx)。

## <a name="connect-to-the-user-database-or-azure-synapse-by-using-ssms-or-ssdt"></a>使用 SSMS 或 SSDT 連接到使用者資料庫或 Azure Synapse  

若要確認 Azure AD 系統管理員已正確設定，請使用 Azure AD 系統管理員帳戶連接到 **master** 資料庫。
若要佈建以 Azure AD 為基礎的自主資料庫使用者 (而非擁有資料庫的伺服器系統管理員)，請利用有權存取資料庫的 Azure AD 身分識別連線到資料庫。

> [!IMPORTANT]
> Visual Studio 2015 中的 [SQL Server 2016 Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) 和 [SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx) 提供 Azure Active Directory 驗證支援。 SSMS 的 2016 年 8 月版本也支援 Active Directory 通用驗證，讓系統管理員能夠使用電話、簡訊、含有 PIN 的智慧卡或行動應用程式通知來要求 Multi-Factor Authentication。

## <a name="using-an-azure-ad-identity-to-connect-using-ssms-or-ssdt"></a>使用 Azure AD 身分識別以使用 SSMS 或 SSDT 進行連線

下列程序會示範如何使用 SQL Server Management Studio 或 SQL Server 資料庫工具的 Azure AD 身分連接到 SQL 資料庫。 

### <a name="active-directory-integrated-authentication"></a>Active Directory 整合驗證

如果您使用來自同盟網域的 Azure Active Directory 認證登入 Windows，或是已針對傳遞和密碼雜湊驗證設定無縫單一登入的受控網域，請使用此方法。 如需詳細資訊，請參閱[Azure Active Directory 無縫單一登入](../active-directory/hybrid/how-to-connect-sso.md)。

1. 啟動 Management Studio 或 Data Tools，然後在 [連線**到伺服器**] （或 **[連接到資料庫引擎**）] 對話方塊的 [**驗證**] 方塊中，選取 [ **Azure Active Directory 整合**]。 不需要密碼或沒有密碼可輸入，因為現有的認證將會在連接時出現。

    ![選取 AD 整合式驗證][11]

2. 選取 [選項]**** 按鈕，然後在 [連線屬性]**** 頁面的 [連線到資料庫]**** 方塊中，鍵入您想要連線的使用者資料庫名稱。 如需詳細資訊，請參閱有關 SSMS 17. x 和 18. x 連線屬性之間差異的[多因素 AAD 驗證](sql-database-ssms-mfa-authentication.md#azure-ad-domain-name-or-tenant-id-parameter)一文。 

    ![選取資料庫名稱][13]

### <a name="active-directory-password-authentication"></a>Active Directory 密碼驗證

使用 Azure AD 受控網域連接到 Azure AD 主體名稱時，請使用這個方法。 您也可以將它用於沒有網域存取權的同盟帳戶，例如在遠端工作時。

使用此方法，透過 Azure AD 僅限雲端的身分識別使用者，或使用 Azure AD 混合式身分識別的人員，向 SQL DB 或 MI 進行驗證。 這個方法支援想要使用其 Windows 認證的使用者，但其本機電腦未加入網域（例如，使用遠端存取）。 在此情況下，Windows 使用者可以指定其網域帳戶和密碼，並可向 SQL DB、MI 或 Azure Synapse 進行驗證。

1. 啟動 Management Studio 或 Data Tools，並在 [**連接到伺服器**] （或 **[連接到資料庫引擎**）] 對話方塊的 [**驗證**] 方塊中，選取 [ **Azure Active Directory-密碼**]。

2. 在 [**使用者名稱**] 方塊中，以**username\@domain.com**的格式輸入您的 Azure Active Directory 使用者名稱。 使用者名稱必須是來自 Azure Active Directory 的帳戶，或是來自具有 Azure Active Directory 之受管理或同盟網域的帳戶。

3. 在 [**密碼**] 方塊中，輸入 Azure Active Directory 帳戶或受管理/同盟網域帳戶的使用者密碼。

    ![選取 AD 密碼驗證][12]

4. 選取 [選項]**** 按鈕，然後在 [連線屬性]**** 頁面的 [連線到資料庫]**** 方塊中，鍵入您想要連線的使用者資料庫名稱。 (請參閱上一個選項中的圖形。)

### <a name="active-directory-interactive-authentication"></a>Active Directory 互動式驗證

使用此方法進行互動式驗證，並搭配或不搭配多重要素驗證（MFA），並以互動方式要求密碼。 這個方法可用來向 SQL DB、MI 和 Azure Synapse 進行驗證，以用於 Azure AD 僅限雲端的身分識別使用者，或使用 Azure AD 混合式身分識別的人員。

如需詳細資訊，請參閱[使用多因素 AAD 驗證搭配 Azure SQL Database 和 Azure Synapse 分析（適用于 MFA 的 SSMS 支援）](sql-database-ssms-mfa-authentication.md)。

## <a name="using-an-azure-ad-identity-to-connect-from-a-client-application"></a>從用戶端應用程式使用 Azure AD 身分識別來連接

下列程序示範如何從用戶端應用程式使用 Azure AD 身分識別連接到 SQL 資料庫。

### <a name="active-directory-integrated-authentication"></a>Active Directory 整合驗證

若要使用整合式 Windows 驗證，您的網域 Active Directory 必須與 Azure Active Directory 同盟，或應該是針對傳遞或密碼雜湊驗證設定為無縫單一登入的受控網域。 如需詳細資訊，請參閱[Azure Active Directory 無縫單一登入](../active-directory/hybrid/how-to-connect-sso.md)。

> [!NOTE]
> 整合式 Windows 驗證的[MSAL.NET （Microsoft. Identity. Client）](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki#roadmap)不支援傳遞和密碼雜湊驗證的無縫單一登入。

連接至資料庫的用戶端應用程式（或服務）必須在已加入網域的電腦上，以使用者的網域認證執行。

若要使用整合式驗證和 Azure AD 的身分識別來連接到資料庫，資料庫連接字串中的驗證關鍵字必須設定`Active Directory Integrated`為。 下列 C# 程式碼範例會使用 ADO.NET。

```csharp
string ConnectionString = @"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Integrated; Initial Catalog=testdb;";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

不支援使用連接字串關鍵字 `Integrated Security=True` 來連接到 Azure SQL Database。 建立 ODBC 連接時，您必須移除空格，並將「驗證」設為 'ActiveDirectoryIntegrated'。

### <a name="active-directory-password-authentication"></a>Active Directory 密碼驗證

若要使用 Azure AD 僅限雲端的身分識別使用者帳戶或使用 Azure AD 混合式身分識別的使用者來連接到資料庫，則必須將`Active Directory Password`驗證關鍵字設定為。 連接字串必須包含使用者識別碼 (UID) 及密碼 (PWD) 關鍵字和值。 下列 C# 程式碼範例會使用 ADO.NET。

```csharp
string ConnectionString =
@"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Password; Initial Catalog=testdb;  UID=bob@contoso.onmicrosoft.com; PWD=MyPassWord!";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

深入了解使用 [Azure AD 驗證 GitHub 示範](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth)上所提供之示範程式碼範例的 Azure AD 驗證方法。

## <a name="azure-ad-token"></a>Azure AD 權杖

此驗證方法可讓仲介層服務取得[JSON Web 權杖（JWT）](../active-directory/develop/id-tokens.md) ，以透過從 AZURE ACTIVE DIRECTORY （AAD）取得權杖來連線到 Azure SQL Database 或 Azure Synapse。 這個方法會啟用各種應用程式案例，包括服務身分識別、服務主體，以及使用以憑證為基礎的驗證的應用程式。 您必須完成四個基本步驟，才能使用 Azure AD 權杖驗證︰

1. 向 Azure Active Directory 註冊您的應用程式，並取得程式碼的用戶端識別碼。
2. 建立代表應用程式的資料庫使用者。 (稍早在步驟 6 中已完成)。
3. 在執行應用程式的用戶端電腦上建立憑證。
4. 將憑證加入應用程式當做索引鍵。

範例連接字串︰

```csharp
string ConnectionString =@"Data Source=n9lxnyuzhv.database.windows.net; Initial Catalog=testdb;"
SqlConnection conn = new SqlConnection(ConnectionString);
conn.AccessToken = "Your JWT token"
conn.Open();
```

如需詳細資訊，請參閱 [SQL Server 安全性部落格](https://blogs.msdn.microsoft.com/sqlsecurity/20../../token-based-authentication-support-for-azure-sql-db-using-azure-ad-auth/)。 如需新增憑證的詳細資訊，請參閱[開始在 Azure Active Directory 中使用憑證式驗證](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md)。

### <a name="sqlcmd"></a>sqlcmd

下列陳述式中使用 sqlcmd 13.1 進行連線，從 [下載中心](https://www.microsoft.com/download/details.aspx?id=53591)即可取得此版本。

> [!NOTE]
> `sqlcmd`使用`-G`命令時，不會使用系統身分識別，而且需要使用者主體登入。

```cmd
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net -G  
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net -U bob@contoso.com -P MyAADPassword -G -l 30
```

## <a name="troubleshooting-azure-ad-authentication"></a>疑難排解 Azure AD 驗證

在下列 blog 中可找到疑難排解 Azure AD 驗證問題的指引：<https://techcommunity.microsoft.com/t5/azure-sql-database/troubleshooting-problems-related-to-azure-ad-authentication-with/ba-p/1062991>

## <a name="next-steps"></a>後續步驟

- 如需 SQL Database 中的登入、使用者、資料庫角色和許可權的總覽，請參閱登入[、使用者、資料庫角色和使用者帳戶](sql-database-manage-logins.md)。
- 如需資料庫主體的詳細資訊，請參閱[主體](https://msdn.microsoft.com/library/ms181127.aspx)。
- 如需資料庫角色的詳細資訊，請參閱[資料庫角色](https://msdn.microsoft.com/library/ms189121.aspx)。
- 如需 SQL Database 中防火牆規則的詳細資訊，請參閱 [SQL Database 防火牆規則](sql-database-firewall-configure.md)。

<!--Image references-->
[1]: ./media/sql-database-aad-authentication/1aad-auth-diagram.png
[2]: ./media/sql-database-aad-authentication/2subscription-relationship.png
[3]: ./media/sql-database-aad-authentication/3admin-structure.png
[4]: ./media/sql-database-aad-authentication/4select-subscription.png
[5]: ./media/sql-database-aad-authentication/5ad-settings-portal.png
[6]: ./media/sql-database-aad-authentication/6edit-directory-select.png
[7]: ./media/sql-database-aad-authentication/7edit-directory-confirm.png
[8]: ./media/sql-database-aad-authentication/8choose-ad.png
[10]: ./media/sql-database-aad-authentication/10choose-admin.png
[11]: ./media/sql-database-aad-authentication/active-directory-integrated.png
[12]: ./media/sql-database-aad-authentication/12connect-using-pw-auth2.png
[13]: ./media/sql-database-aad-authentication/13connect-to-db2.png
