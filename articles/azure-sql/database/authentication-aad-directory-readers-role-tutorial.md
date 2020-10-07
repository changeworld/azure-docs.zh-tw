---
title: 將目錄讀取者角色指派給 Azure AD 群組並管理角色指派
description: 本文會逐步引導您使用 Azure AD 群組來啟用目錄讀取者角色，以透過 Azure SQL Database、Azure SQL 受控執行個體和 Azure Synapse Analytics 來管理 Azure AD 角色指派
ms.service: sql-db-mi
ms.subservice: security
ms.custom: azure-synapse
ms.topic: tutorial
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 08/14/2020
ms.openlocfilehash: d6c447deedbdcc4f2439fc069f368db88b3560b9
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91278017"
---
# <a name="tutorial-assign-directory-readers-role-to-an-azure-ad-group-and-manage-role-assignments"></a>教學課程：將目錄讀取者角色指派給 Azure AD 群組並管理角色指派

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

> [!NOTE]
> 在本文中，群組的**目錄讀者**角色指派為**公開預覽**。 

本文會引導您在 Azure Active Directory (Azure AD) 中建立群組，並將[**目錄讀者**](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#directory-readers)角色指派給該群組。 目錄讀者權限允許群組擁有者將其他成員新增至群組，例如 [Azure SQL Database](sql-database-paas-overview.md) 的 [受控識別](../../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types)、[Azure SQL 受控執行個體](../managed-instance/sql-managed-instance-paas-overview.md)，以及 [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)。 如此就不需要[全域管理員](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator)或[特殊權限角色管理員](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)，即直接為租用戶中的每個 Azure SQL 邏輯伺服器身分識別指派目錄讀者角色。

本教學課程使用[在 Azure Active Directory (預覽) 中使用雲端群組管理角色指派](../../active-directory/users-groups-roles/roles-groups-concept.md)中介紹的功能。 

如需了解將目錄讀者角色指派給 Azure SQL Azure AD 群組有何優點的詳細資訊，請參閱 [Azure SQL 的 Azure Active Directory 中目錄讀者角色](authentication-aad-directory-readers-role.md)。

## <a name="prerequisites"></a>先決條件

- Azure AD 執行個體。 如需詳細資訊，請參閱 [使用 Azure SQL 設定和管理 Azure AD 驗證](authentication-aad-configure.md)。
- SQL Database、SQL 受控執行個體或 Azure Synapse。

## <a name="directory-readers-role-assignment-using-the-azure-portal"></a>使用 Azure 入口網站的目錄讀者角色指派

### <a name="create-a-new-group-and-assign-owners-and-role"></a>建立新的群組，並指派擁有者和角色

1. 具有[全域管理員](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator)或[特殊權限角色管理員](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)權限的使用者必須具備初始設定的權限。
1. 讓特殊權限使用者登入 [Azure 入口網站](https://portal.azure.com)。
1. 移至 **Azure Active Directory** 資源。 在**受控**下，移至**群組**。 若要建立新群組，請選取 [新增群組]。
1. 選取 [**安全性**] 作為群組類型，並填入其餘欄位。 請確定 **Azure AD 角色可指派給群組 (預覽)** 設定已切換為 **是**。 然後將 Azure AD **目錄讀者**角色指派給群組。
1. 將 Azure AD 使用者指派為所建立之群組的擁有者。 群組擁有者可以是一般的 AD 使用者，系統不會指派任何 Azure AD 管理角色。 擁有者應該是管理您 SQL Database、SQL 受控執行個體或 Azure Synapse 的使用者。

   :::image type="content" source="media/authentication-aad-directory-readers-role/new-group.png" alt-text="aad-new-group":::

1. 選取 [建立] 

### <a name="checking-the-group-that-was-created"></a>檢查已建立的群組

> [!NOTE]
> 請確定**群組類型**為**安全性**。 Azure SQL 不支援 Microsoft 365 群組。

若要檢查和管理已建立的群組，請回到 Azure 入口網站中的**群組**窗格，然後搜尋您的群組名稱。 選取群組之後，您可以在**擁有者**和**管理**設定的**成員**功能表下新增其他擁有者和成員。 您也可以檢閱群組的**已指派角色**。

:::image type="content" source="media/authentication-aad-directory-readers-role/azure-ad-group-created.png" alt-text="aad-new-group":::

### <a name="add-azure-sql-managed-identity-to-the-group"></a>將 Azure SQL 受控識別新增至群組

> [!NOTE]
> 在此範例中，我們會使用 SQL 受控執行個體，但可以針對 SQL Database 或 Azure Synapse 套用類似的步驟，以達到相同的結果。

在後續步驟中，已不再需要全域管理員或特殊權限角色管理員使用者。

1. 以管理 SQL 受控執行個體的使用者身分 (也就是是稍早建立之群組的擁有者)，登入 Azure 入口網站。

1. 在 Azure 入口網站中尋找 **SQL 受控執行個體**資源。

   :::image type="content" source="media/authentication-aad-directory-readers-role/azure-ad-managed-instance.png" alt-text="aad-new-group":::

   在建立 SQL 受控執行個體期間，系統會為您的執行個體建立 Azure 身分識別。 建立的身分識別與 SQL 受控執行個體名稱具有相同的前置詞。 您可以遵循下列步驟，找到建立為 Azure AD 應用程式之 SQL 受控執行個體身分識別的服務主體：

    - 移至 **Azure Active Directory** 資源。 在**管理**下選取 [企業應用程式]。 **物件識別碼**是執行個體的身分識別。
    
    :::image type="content" source="media/authentication-aad-directory-readers-role/azure-ad-managed-instance-service-principal.png" alt-text="aad-new-group":::

1. 移至 **Azure Active Directory** 資源。 在**受控**下，移至**群組**。 選取您建立的群組。 在群組的**受控**設定下，選取 [成員]。 選取 [新增成員]，然後藉由搜尋上方找到的名稱，將您的 SQL 受控執行個體服務主體新增為群組的成員。

   :::image type="content" source="media/authentication-aad-directory-readers-role/azure-ad-add-managed-instance-service-principal.png" alt-text="aad-new-group":::

> [!NOTE]
> 可能需要幾分鐘的時間，才能透過 Azure 系統傳播服務主體權限，並允許存取 Azure AD 圖形 API。 在佈建 SQL 受控執行個體的 Azure AD 管理員之前，您可能必須等候幾分鐘的時間。

### <a name="remarks"></a>備註

針對 SQL Database 和 Azure Synapse，您可以在建立 Azure SQL 邏輯伺服器期間或在建立伺服器之後，建立伺服器身分識別。 如需有關如何在 SQL Database 或 Azure Synapse 中建立或設定伺服器身分識別的詳細資訊，請參閱[啟用服務主體以建立 Azure AD 的使用者](authentication-aad-service-principal.md#enable-service-principals-to-create-azure-ad-users)。

針對 SQL 受控執行個體，必須先將**目錄讀者**角色指派給受控執行個體身分識別，才能[設定受控執行個體的 Azure AD 管理員](authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance)。

設定邏輯伺服器的 Azure AD 管理員時，對於 SQL Database 或 Azure Synapse，不需要將**目錄讀者**角色指派給伺服器身分識別。 不過，若要在 SQL Database 或 Azure Synapse 中代表 Azure AD 應用程式啟用 Azure AD 物件建立，則需要**目錄讀者**角色。 如果角色未指派給 SQL 邏輯伺服器身分識別，將無法在 Azure SQL 中建立 Azure AD 使用者。 如需詳細資訊，請參閱[使用 Azure SQL 的 Azure Active Directory 服務主體](authentication-aad-service-principal.md)。

## <a name="directory-readers-role-assignment-using-powershell"></a>使用 PowerShell 的目錄讀者角色指派

> [!IMPORTANT]
> [全域管理員](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator)或[特殊權限角色管理員](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)需要執行這些初始步驟。 除了 PowerShell 之外，Azure AD 提供 Microsoft 圖形 API [可在 Azure AD 中建立可指派角色的群組](../../active-directory/users-groups-roles/roles-groups-create-eligible.md#using-microsoft-graph-api)。

1. 使用下列命令下載 Azure AD Preview PowerShell 模組。 您必須以管理員身分執行 PowerShell。

    ```powershell
    Install-Module azureadpreview
    Import-Module azureadpreview
    #To verify that the module is ready to use, use the following command:
    Get-Module azureadpreview
    ```

1. 連線至您的 Azure AD 租用戶。

    ```powershell
    Connect-AzureAD
    ```

1. 建立一個安全性群組，以指派**目錄讀者**角色。

    - `DirectoryReaderGroup`、`Directory Reader Group` 和 `DirRead` 可以根據您的喜好設定變更。

    ```powershell
    $group = New-AzureADMSGroup -DisplayName "DirectoryReaderGroup" -Description "Directory Reader Group" -MailEnabled $False -SecurityEnabled $true -MailNickName "DirRead" -IsAssignableToRole $true
    $group
    ```

1. 將**目錄讀者**角色指派給群組。

    ```powershell
    # Displays the Directory Readers role information
    $roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Directory Readers'" 
    $roleDefinition
    ```

    ```powershell
    # Assigns the Directory Readers role to the group
    $roleAssignment = New-AzureADMSRoleAssignment -ResourceScope '/' -RoleDefinitionId $roleDefinition.Id -PrincipalId $group.Id
    $roleAssignment
    ```

1. 將擁有者指派給群組。

    - 以您想要擁有此群組的使用者取代 `<username>`。 您可以重複這些步驟來新增數個擁有者。

    ```powershell
    $RefObjectID = Get-AzureADUser -ObjectId "<username>"
    $RefObjectID
    ```

    ```powershell
    $GrOwner = Add-AzureADGroupOwner -ObjectId $group.ID -RefObjectId $RefObjectID.ObjectID
    ```

    使用下列命令檢查群組的擁有者：

    ```powershell
    Get-AzureADGroupOwner -ObjectId $group.ID
    ```

    您也可以在 [Azure 入口網站](https://portal.azure.com)中驗證群組的擁有者。 遵循[檢查已建立的群組](#checking-the-group-that-was-created)中的步驟。

### <a name="assigning-the-service-principal-as-a-member-of-the-group"></a>將服務主體指派為群組的成員

在後續步驟中，已不再需要全域管理員或特殊權限角色管理員使用者。

1. 使用群組的擁有者 (也會管理 Azure SQL 資源)，執行下列命令以連線到您的 Azure AD。

    ```powershell
    Connect-AzureAD
    ```

1. 將服務主體指派為已建立之群組的成員。

    - 以您的 Azure SQL 邏輯伺服器名稱或受控執行個體名稱取代 `<ServerName>`。 如需詳細資訊，請參閱[將 Azure SQL 服務識別新增至群組](#add-azure-sql-managed-identity-to-the-group)一節

    ```powershell
    # Returns the service principal of your Azure SQL resource
    $miIdentity = Get-AzureADServicePrincipal -SearchString "<ServerName>"
    $miIdentity
    ```

    ```powershell
    # Adds the service principal to the group as a member
    Add-AzureADGroupMember -ObjectId $group.ID -RefObjectId $miIdentity.ObjectId 
    ```

    下列命令會傳回服務主體物件識別碼，指出已新增至群組：

    ```powershell
    Add-AzureADGroupMember -ObjectId $group.ID -RefObjectId $miIdentity.ObjectId
    ```

## <a name="next-steps"></a>後續步驟

- [Azure Active Directory 中適用於 Azure SQL 的 Directory 讀者角色](authentication-aad-directory-readers-role.md)
- [教學課程：使用 Azure AD 應用程式建立 Azure AD 使用者](authentication-aad-service-principal-tutorial.md)
- [使用 Azure SQL 設定和管理 Azure AD 驗證](authentication-aad-configure.md)