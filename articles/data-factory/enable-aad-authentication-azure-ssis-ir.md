---
title: 為 Azure SSIS 整合執行時開啟 AAD
description: 本文說明如何使用 Azure Data Factory 的受控識別啟用 Azure Active Directory 驗證，來建立 Azure-SSIS Integration Runtime。
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.devlang: powershell
ms.topic: conceptual
author: swinarko
ms.author: sawinark
manager: mflasko
ms.custom: seo-lt-2019
ms.date: 5/14/2019
ms.openlocfilehash: 2359b378b1f54cf6e03218f819b3a7c5740ba596
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416396"
---
# <a name="enable-azure-active-directory-authentication-for-azure-ssis-integration-runtime"></a>啟用適用於 Azure-SSIS Integration Runtime 的 Azure Active Directory 驗證

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

本文介紹如何使用 Azure 資料工廠 (ADF) 的託管識別啟用 Azure 活動目錄 (Azure AD) 身份驗證,並將其而不是傳統的身份驗證方法(如 SQL 身份驗證)用於:

- 創建 Azure-SSIS 整合執行時 (IR),該執行時將反過來代表您在 Azure SQL 資料庫伺服器/託管實例中預配 SSIS 目錄資料庫 (SSISDB)。

- 在 Azure-SSIS IR 上運行 SSIS 包時連接到各種 Azure 資源。

有關 ADF 的託管識別的詳細資訊,請參閱[資料工廠的託管識別](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity)。

> [!NOTE]
>-  在這種情況下,具有 ADF 託管標識的 Azure AD 身份驗證僅用於創建和後續啟動 SSIS IR 的操作,該操作反過來將預配並連接到 SSISDB。 對於 SSIS 包執行,SSIS IR 仍將使用 SQL 身份驗證連接到 SSISDB,這些帳戶在 SSISDB 預配期間創建。
>-  如果您已經使用 SQL 身份驗證創建了 SSIS IR,則無法此時將其重新配置為透過 PowerShell 使用 Azure AD 身份驗證,但可以通過 Azure 入口/ADF 應用執行此操作。 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="enable-azure-ad-on-azure-sql-database"></a>在 Azure SQL Database 上啟用 Azure AD

Azure SQL Database 伺服器支援由 Azure AD 使用者建立資料庫。 首先，您需要以成員的身分，使用您 ADF 的受控識別，建立 Azure AD 群組。 接著，您需要將 Azure AD 使用者設定為 Azure SQL Database 伺服器的 Active Directory 管理員，然後在 SQL Server Management Studio (SSMS) 上將該使用者連線到伺服器。 最後，您需要建立一個代表 Azure AD 群組的內含使用者，以便 Azure-SSIS IR 可以使用您 ADF 的受控識別，代表您建立 SSISDB。

### <a name="create-an-azure-ad-group-with-the-managed-identity-for-your-adf-as-a-member"></a>以成員的身分使用您 ADF 的受控識別，建立 Azure AD 群組

您可以使用現有的 Azure AD 群組，或使用 Azure AD PowerShell 建立一個新群組。

1.  安裝[Azure AD 電源外殼](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2)模組。

2.  使用  `Connect-AzureAD` 登入，執行下列 Cmdlet 以建立群組，並將其儲存在變數中：

    ```powershell
    $Group = New-AzureADGroup -DisplayName "SSISIrGroup" `
                              -MailEnabled $false `
                              -SecurityEnabled $true `
                              -MailNickName "NotSet"
    ```

    結果如下列範例所示，同時也顯示變數值：

    ```powershell
    $Group

    ObjectId DisplayName Description
    -------- ----------- -----------
    6de75f3c-8b2f-4bf4-b9f8-78cc60a18050 SSISIrGroup
    ```

3.  將 ADF 的受控識別新增至群組。 您可以按照[資料工廠的託管標識](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity)來獲取主託管標識物件 ID(例如 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc,但不為此使用託管標識應用程式 ID)。

    ```powershell
    Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
    ```

    您也可以之後再檢查群組成員資格。

    ```powershell
    Get-AzureAdGroupMember -ObjectId $Group.ObjectId
    ```

### <a name="configure-azure-ad-authentication-for-azure-sql-database-server"></a>設定 Azure SQL Database 伺服器的 Azure AD 驗證

您可以使用下列步驟， [透過 SQL 設定及管理 Azure AD 驗證](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure)：

1.  在 Azure 門戶中,從左側導航中選擇**所有服務** -> **SQL 伺服器**。

2.  選取要使用 Azure AD 驗證設定的 Azure SQL Database 伺服器。

3.  在刀鋒視窗的 [設定]**** 區段中，選取 [Active Directory 管理員]****。

4.  在命令列中選取 [設定管理員]****。

5.  選擇要成為伺服器管理員的 Azure AD 使用者帳戶,然後選擇 **"選擇"**

6.  在命令列中選取 [儲存]****。

### <a name="create-a-contained-user-in-azure-sql-database-server-representing-the-azure-ad-group"></a>在 Azure SQL Database 伺服器中，建立一個代表 Azure AD 群組的內含使用者

對於下一步,您需要 [Microsoft SQL 伺服器管理工作室](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS)。

1. 啟動 SSMS。

2. 在「**連接到伺服器」** 對話框中,在 **「伺服器名稱**」欄位中輸入 Azure SQL 資料庫伺服器名稱。

3. 在 **「身份驗證**」欄位中,選擇 **「活動目錄- 通用」支援 MFA(** 您還可以使用其他兩種活動目錄身份驗證類型,請參閱[使用 SQL 設定和管理 Azure AD 身份驗證](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure))。

4. 在 **「使用者名稱」** 欄位中,輸入您作為伺服器管理員設定的 Azure AD 帳號的testuser@xxxonline.com名稱,例如 。

5. 選擇**連接**並完成登錄過程。

6. 在 [物件總管]**** 中，展開 [資料庫]****[系統資料庫] -> **** 資料夾。

7. 在 [master]**** 資料庫上按一下滑鼠右鍵，然後選取 [新增查詢]****。

8. 在查詢視窗中,輸入以下 T-SQL 命令,並在工具列上選擇 **「執行**」。

   ```sql
   CREATE USER [SSISIrGroup] FROM EXTERNAL PROVIDER
   ```

   命令應該會順利完成，建立代表群組的內含使用者。

9. 清除查詢視窗，輸入下列 T-SQL 命令，然後在工具列中選取 [執行]****。

   ```sql
   ALTER ROLE dbmanager ADD MEMBER [SSISIrGroup]
   ```

   命令應該會順利完成，將建立資料庫 (SSISDB) 的能力授與內含的使用者。

10. 如果 SSISDB 是使用 SQL 身份驗證建立的,並且希望切換到使用 Azure-SSIS IR 的 Azure AD 身份驗證來存取它,請右鍵單擊**SSISDB**資料庫並選擇 **"新建查詢**"。

11. 在查詢視窗中,輸入以下 T-SQL 命令,並在工具列上選擇 **「執行**」。

    ```sql
    CREATE USER [SSISIrGroup] FROM EXTERNAL PROVIDER
    ```

    命令應該會順利完成，建立代表群組的內含使用者。

12. 清除查詢視窗，輸入下列 T-SQL 命令，然後在工具列中選取 [執行]****。

    ```sql
    ALTER ROLE db_owner ADD MEMBER [SSISIrGroup]
    ```

    命令應該會順利完成，將存取 SSISDB 的能力授與內含的使用者。

## <a name="enable-azure-ad-on-azure-sql-database-managed-instance"></a>為 Azure SQL Database 受控執行個體啟用 Azure AD

「Azure SQL Database 受控執行個體」支援直接使用 ADF 的受控識別來建立資料庫。 您不需要將 ADF的受控識別加入 Azure AD 群組，也不需要在受控執行個體中建立代表該群組的內含使用者。

### <a name="configure-azure-ad-authentication-for-azure-sql-database-managed-instance"></a>為 Azure SQL Database 受控執行個體設定 Azure AD 驗證

按照[為託管實例預配 Azure 活動目錄管理員](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure#provision-an-azure-active-directory-administrator-for-your-managed-instance)的步驟。

### <a name="add-the-managed-identity-for-your-adf-as-a-user-in-azure-sql-database-managed-instance"></a>在 Azure SQL Database 受控執行個體中，以使用者身分為 ADF 新增受控識別

對於下一步,您需要 [Microsoft SQL 伺服器管理工作室](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS)。

1.  啟動 SSMS。

2.  使用**系統管理員**的 SQL Server 帳戶連接到託管實例。 這是一個臨時限制,一旦 Azure SQL 資料庫託管實例的 Azure AD 伺服器主體(登錄)成為 GA,將刪除該限制。 如果您嘗試使用 Azure AD 管理員帳戶建立登錄名:Msg 15247、級別 16、狀態 1、行 1 使用者無權執行此操作,您將看到以下錯誤。

3.  在 [物件總管]**** 中，展開 [資料庫]****[系統資料庫] -> **** 資料夾。

4.  在 [master]**** 資料庫上按一下滑鼠右鍵，然後選取 [新增查詢]****。

5.  在查詢視窗中,執行以下 T-SQL 文稿以新增 ADF 作為使用者的託管識別

    ```sql
    CREATE LOGIN [{your ADF name}] FROM EXTERNAL PROVIDER
    ALTER SERVER ROLE [dbcreator] ADD MEMBER [{your ADF name}]
    ALTER SERVER ROLE [securityadmin] ADD MEMBER [{your ADF name}]
    ```
    
    命令應該會順利完成，將建立資料庫 (SSISDB) 的能力授與 ADF 的受控識別。

6.  如果 SSISDB 是使用 SQL 身份驗證建立的,並且希望切換到使用 Azure-SSIS IR 的 Azure AD 身份驗證來存取它,請右鍵單擊**SSISDB**資料庫並選擇 **"新建查詢**"。

7.  在查詢視窗中,輸入以下 T-SQL 命令,並在工具列上選擇 **「執行**」。

    ```sql
    CREATE USER [{your ADF name}] FOR LOGIN [{your ADF name}] WITH DEFAULT_SCHEMA = dbo
    ALTER ROLE db_owner ADD MEMBER [{your ADF name}]
    ```

    命令應該會順利完成，將存取 SSISDB 的能力授與 ADF 的受控識別。

## <a name="provision-azure-ssis-ir-in-azure-portaladf-app"></a>在 Azure 入口網站/ADF 應用程式中，佈建 Azure-SSIS IR

當您在 Azure 入口網站/ADF 應用程式中佈建 Azure-SSIS IR 時，請在 [SQL 設定]**** 頁面上，選取 [對您的 ADF 使用 AAD 驗證搭配受控識別]**** 選項。 下列螢幕擷取畫面顯示裝載 SSISDB 之 Azure SQL Database 伺服器的整合執行階段設定。 對於具有裝載 SSISDB 之受控執行個體的整合執行階段，**目錄資料庫服務層**和**允許 Azure 服務存取** 設定不適用，而其他設定皆相同。

如需關於如何建立 Azure-SSIS IR 的詳細資訊，請參閱[在 Azure Data Factory 中建立 Azure-SSIS 整合執行階段](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)。

![Azure-SSIS 整合執行階段的設定](media/enable-aad-authentication-azure-ssis-ir/enable-aad-authentication.png)

## <a name="provision-azure-ssis-ir-with-powershell"></a>使用 PowerShell 佈建 Azure-SSIS IR

若要使用 PowerShell 佈建 Azure-SSIS IR，請執行下列作業：

1.  安裝[Azure 電源外殼](https://github.com/Azure/azure-powershell/releases/tag/v5.5.0-March2018) 模組。

2.  在您的指令碼中，請勿設定 `CatalogAdminCredential` 參數。 例如：

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -Description $AzureSSISDescription `
                                               -Type Managed `
                                               -Location $AzureSSISLocation `
                                               -NodeSize $AzureSSISNodeSize `
                                               -NodeCount $AzureSSISNodeNumber `
                                               -Edition $AzureSSISEdition `
                                               -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                               -CatalogServerEndpoint $SSISDBServerEndpoint `
                                               -CatalogPricingTier $SSISDBPricingTier

    Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                                 -DataFactoryName $DataFactoryName `
                                                 -Name $AzureSSISName
    ```

## <a name="run-ssis-packages-with-managed-identity-authentication"></a>使用託管識別身分驗證執行 SSIS 套件

在 Azure-SSIS IR 上運行 SSIS 包時,可以使用託管識別身分驗證連接到各種 Azure 資源。 目前,我們已經在以下連接管理器中支援託管身份身份驗證。

- [OLE DB 連接管理員](https://docs.microsoft.com/sql/integration-services/connection-manager/ole-db-connection-manager#managed-identities-for-azure-resources-authentication)

- [ADO.NET 連接管理員](https://docs.microsoft.com/sql/integration-services/connection-manager/ado-net-connection-manager#managed-identities-for-azure-resources-authentication)

- [Azure 儲存體連線管理員](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-storage-connection-manager#managed-identities-for-azure-resources-authentication)
