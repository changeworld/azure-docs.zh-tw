---
title: 啟用 Azure SSIS 的 AAD Integration Runtime
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
ms.openlocfilehash: 77b49d8446c7882a155742e8455d77bd1ec110cb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81606198"
---
# <a name="enable-azure-active-directory-authentication-for-azure-ssis-integration-runtime"></a>啟用適用於 Azure-SSIS Integration Runtime 的 Azure Active Directory 驗證

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文說明如何使用適用于您 Azure Data Factory （ADF）的受控識別來啟用 Azure Active Directory （Azure AD）驗證，並使用它來取代傳統驗證方法（例如 SQL 驗證）來執行下列動作：

- 建立 Azure SSIS Integration Runtime （IR），以代表您在 Azure SQL Database server/受控執行個體中提供 SSIS 目錄資料庫（SSISDB）。

- 在 Azure SSIS IR 上執行 SSIS 套件時，連接到各種 Azure 資源。

如需 ADF 受控識別的詳細資訊，請參閱[Data Factory 的受控識別](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity)。

> [!NOTE]
>-  在此案例中，使用您 ADF 的受控識別進行 Azure AD 驗證時，只會用於 SSIS IR 的建立和後續啟動作業，然後再布建和連線至 SSISDB。 針對 SSIS 套件執行，您的 SSIS IR 仍然會使用 SQL 驗證搭配 ssisdb 布建期間所建立且完全受控的帳戶來連線至 SSISDB。
>-  如果您已經使用 SQL 驗證建立 SSIS IR，您就無法將它重新設定為透過 PowerShell 使用 Azure AD 驗證，但是您可以透過 Azure 入口網站/ADF 應用程式來這麼做。 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="enable-azure-ad-on-azure-sql-database"></a>在 Azure SQL Database 上啟用 Azure AD

Azure SQL Database 伺服器支援由 Azure AD 使用者建立資料庫。 首先，您需要以成員的身分，使用您 ADF 的受控識別，建立 Azure AD 群組。 接著，您需要將 Azure AD 使用者設定為 Azure SQL Database 伺服器的 Active Directory 管理員，然後在 SQL Server Management Studio (SSMS) 上將該使用者連線到伺服器。 最後，您需要建立一個代表 Azure AD 群組的內含使用者，以便 Azure-SSIS IR 可以使用您 ADF 的受控識別，代表您建立 SSISDB。

### <a name="create-an-azure-ad-group-with-the-managed-identity-for-your-adf-as-a-member"></a>以成員的身分使用您 ADF 的受控識別，建立 Azure AD 群組

您可以使用現有的 Azure AD 群組，或使用 Azure AD PowerShell 建立一個新群組。

1.  安裝[Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2)模組。

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

3.  將 ADF 的受控識別新增至群組。 您可以遵循 Data Factory 的[受控識別](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity)一文來取得主體受控識別物件識別碼（例如 765ad4ab-xxxx-xxxx-xxxx-51ed985819dc-xxxx-xxxx 765ad4ab-xxxx-xxxx-xxxx-51ed985819dc，但不要使用受控識別應用程式識別碼來達到此目的）。

    ```powershell
    Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
    ```

    您也可以之後再檢查群組成員資格。

    ```powershell
    Get-AzureAdGroupMember -ObjectId $Group.ObjectId
    ```

### <a name="configure-azure-ad-authentication-for-azure-sql-database-server"></a>設定 Azure SQL Database 伺服器的 Azure AD 驗證

您可以使用下列步驟， [透過 SQL 設定及管理 Azure AD 驗證](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure)：

1.  在 Azure 入口網站中，從左側導覽中選取 [**所有服務** -> ]**[SQL 伺服器**]。

2.  選取要使用 Azure AD 驗證設定的 Azure SQL Database 伺服器。

3.  在刀鋒視窗的 [設定]**** 區段中，選取 [Active Directory 管理員]****。

4.  在命令列中選取 [設定管理員]****。

5.  選取要設為伺服器系統管理員的 Azure AD 使用者帳戶，然後選取 [**選取]。**

6.  在命令列中選取 [儲存]****。

### <a name="create-a-contained-user-in-azure-sql-database-server-representing-the-azure-ad-group"></a>在 Azure SQL Database 伺服器中，建立一個代表 Azure AD 群組的內含使用者

在下一個步驟中，您需要 [Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) （SSMS）。

1. 啟動 SSMS。

2. 在 [**連接到伺服器**] 對話方塊的 [**伺服器名稱**] 欄位中，輸入您的 Azure SQL Database 伺服器名稱。

3. 在 [**驗證**] 欄位中，選取 [**具有 MFA 支援的 Active Directory-通用**] （您也可以使用其他兩個 Active Directory 驗證類型，請參閱[設定及管理使用 SQL 的 Azure AD 驗證](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure)）。

4. 在 [**使用者名稱**] 欄位中，輸入您設定為伺服器管理員之 Azure AD 帳戶的名稱，例如testuser@xxxonline.com。

5. 選取 **[** 連線並完成登入程式]。

6. 在 [物件總管]**** 中，展開 [資料庫]****[系統資料庫] -> **** 資料夾。

7. 在 [master]**** 資料庫上按一下滑鼠右鍵，然後選取 [新增查詢]****。

8. 在查詢視窗中，輸入下列 T-sql 命令，然後在工具列上選取 [**執行**]。

   ```sql
   CREATE USER [SSISIrGroup] FROM EXTERNAL PROVIDER
   ```

   命令應該會順利完成，建立代表群組的內含使用者。

9. 清除查詢視窗，輸入下列 T-SQL 命令，然後在工具列中選取 [執行]****。

   ```sql
   ALTER ROLE dbmanager ADD MEMBER [SSISIrGroup]
   ```

   命令應該會順利完成，將建立資料庫 (SSISDB) 的能力授與內含的使用者。

10. 如果您的 SSISDB 是使用 SQL 驗證建立的，而您想要切換為使用 Azure AD authentication 來讓您的 Azure SSIS IR 存取它，請以滑鼠右鍵按一下**ssisdb**資料庫，然後選取 [追加**查詢**]。

11. 在查詢視窗中，輸入下列 T-sql 命令，然後在工具列上選取 [**執行**]。

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

請遵循[為您的受控執行個體布建 Azure Active Directory 系統管理員](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure#provision-an-azure-active-directory-administrator-for-your-managed-instance)中的步驟。

### <a name="add-the-managed-identity-for-your-adf-as-a-user-in-azure-sql-database-managed-instance"></a>在 Azure SQL Database 受控執行個體中，以使用者身分為 ADF 新增受控識別

在下一個步驟中，您需要 [Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) （SSMS）。

1.  啟動 SSMS。

2.  使用屬於**系統管理員（sysadmin**）的 SQL Server 帳戶，連接到您的受控執行個體。 這是暫時性的限制，一旦 Azure SQL Database 受控執行個體的伺服器主體（登入）變成 GA，就會移除 Azure AD 一次。 如果您嘗試使用 Azure AD 系統管理員帳戶來建立登入，則會看到下列錯誤：訊息15247、層級16、狀態1、第1行使用者沒有執行此動作的許可權。

3.  在 [物件總管]**** 中，展開 [資料庫]****[系統資料庫] -> **** 資料夾。

4.  在 [master]**** 資料庫上按一下滑鼠右鍵，然後選取 [新增查詢]****。

5.  在查詢視窗中，執行下列 T-sql 腳本，以使用者身分新增 ADF 的受控識別

    ```sql
    CREATE LOGIN [{your ADF name}] FROM EXTERNAL PROVIDER
    ALTER SERVER ROLE [dbcreator] ADD MEMBER [{your ADF name}]
    ALTER SERVER ROLE [securityadmin] ADD MEMBER [{your ADF name}]
    ```
    
    命令應該會順利完成，將建立資料庫 (SSISDB) 的能力授與 ADF 的受控識別。

6.  如果您的 SSISDB 是使用 SQL 驗證建立的，而您想要切換為使用 Azure AD authentication 來讓您的 Azure SSIS IR 存取它，請以滑鼠右鍵按一下**ssisdb**資料庫，然後選取 [追加**查詢**]。

7.  在查詢視窗中，輸入下列 T-sql 命令，然後在工具列上選取 [**執行**]。

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

1.  安裝[Azure PowerShell](https://github.com/Azure/azure-powershell/releases/tag/v5.5.0-March2018) 模組。

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

## <a name="run-ssis-packages-with-managed-identity-authentication"></a>使用受控識別驗證執行 SSIS 套件

當您在 Azure SSIS IR 上執行 SSIS 套件時，您可以使用受控識別驗證來連接到各種 Azure 資源。 目前我們已支援下列連線管理員中的受控識別驗證。

- [OLE DB 連接管理員](https://docs.microsoft.com/sql/integration-services/connection-manager/ole-db-connection-manager#managed-identities-for-azure-resources-authentication)

- [ADO.NET 連接管理員](https://docs.microsoft.com/sql/integration-services/connection-manager/ado-net-connection-manager#managed-identities-for-azure-resources-authentication)

- [Azure 儲存體連線管理員](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-storage-connection-manager#managed-identities-for-azure-resources-authentication)
