---
title: 為 Azure SSIS 啟用 AAD Integration Runtime
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
ms.date: 07/09/2020
ms.openlocfilehash: 30f5b5990e189cb6942c15b65b6a417ce49f0c2b
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2020
ms.locfileid: "92637797"
---
# <a name="enable-azure-active-directory-authentication-for-azure-ssis-integration-runtime"></a>啟用適用於 Azure-SSIS Integration Runtime 的 Azure Active Directory 驗證

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

本文說明如何使用 Azure Data Factory (ADF) 的受控識別來啟用 Azure Active Directory (Azure AD) 驗證，並使用它來取代傳統驗證方法 (例如 SQL 驗證) 至：

- 建立 Azure-SSIS Integration Runtime (IR) ，以代表您將 SSIS 目錄資料庫 (SSISDB) SQL Database 或 SQL 受控執行個體中提供。

- 在 Azure-SSIS IR 上執行 SSIS 套件時，連接到各種 Azure 資源。

如需 ADF 受控識別的詳細資訊，請參閱 [Data Factory 的受控識別](./data-factory-service-identity.md)。

> [!NOTE]
>
> - 在此案例中，使用 ADF 的受控識別進行 Azure AD 驗證只會用於 SSIS IR 的建立和後續啟動作業，進而布建及連線到 SSISDB。 針對 SSIS 套件執行，您的 SSIS IR 仍會使用 SQL 驗證搭配 SSISDB 布建期間所建立的完全受控帳戶來連線至 SSISDB。
> - 如果您已經使用 SQL 驗證建立 SSIS IR，您目前無法將它重新設定為使用 Azure AD 透過 PowerShell 進行驗證，但您可以透過 Azure 入口網站/ADF 應用程式來執行此作業。 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="enable-azure-ad-on-azure-sql-database"></a>在 Azure SQL Database 上啟用 Azure AD

SQL Database 支援以 Azure AD 使用者建立資料庫。 首先，您需要以成員的身分，使用您 ADF 的受控識別，建立 Azure AD 群組。 接下來，您必須將 Azure AD 使用者設定為 SQL Database 的 Active Directory 系統管理員，然後使用該使用者 SQL Server Management Studio (SSMS) 連接到該使用者。 最後，您需要建立一個代表 Azure AD 群組的內含使用者，以便 Azure-SSIS IR 可以使用您 ADF 的受控識別，代表您建立 SSISDB。

### <a name="create-an-azure-ad-group-with-the-managed-identity-for-your-adf-as-a-member"></a>以成員的身分使用您 ADF 的受控識別，建立 Azure AD 群組

您可以使用現有的 Azure AD 群組，或使用 Azure AD PowerShell 建立一個新群組。

1.  安裝 [Azure AD PowerShell](/powershell/azure/active-directory/install-adv2) 模組。

2.  使用登入 `Connect-AzureAD` ，執行下列 Cmdlet 來建立群組，並將其儲存在變數中：

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

3.  將 ADF 的受控識別新增至群組。 您可以遵循 Data Factory 的「 [受控識別](./data-factory-service-identity.md) 」一文來取得主要受控識別物件識別碼 (例如 765ad4ab-xxxx-xxxx-xxxx-51ed985819dc-xxxx 765ad4ab-xxxx-xxxx-xxxx-51ed985819dc，但請勿針對此用途) 使用受控識別應用程式識別碼。

    ```powershell
    Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
    ```

    您也可以之後再檢查群組成員資格。

    ```powershell
    Get-AzureAdGroupMember -ObjectId $Group.ObjectId
    ```

### <a name="configure-azure-ad-authentication-for-sql-database"></a>設定 SQL Database 的 Azure AD authentication

您可以使用下列步驟， [設定和管理 SQL 的 Azure AD 驗證](../azure-sql/database/authentication-aad-configure.md) ：

1.  在 Azure 入口網站中，從左側導覽中選取 [ **所有服務**  ->  **SQL server** ]。

2.  在 SQL Database 中選取您的伺服器，以 Azure AD 驗證進行設定。

3.  在刀鋒視窗的 [設定]  區段中，選取 [Active Directory 管理員]  。

4.  在命令列中選取 [設定管理員]  。

5.  選取要設為伺服器管理員的 Azure AD 使用者帳戶，然後選取 [選取] **。**

6.  在命令列中選取 [儲存]  。

### <a name="create-a-contained-user-in-sql-database-representing-the-azure-ad-group"></a>在代表 Azure AD 群組的 SQL Database 中建立包含的使用者

在此後續步驟中，您需要 [Microsoft SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS)。

1. 啟動 SSMS。

2. 在 [ **連接到伺服器** ] 對話方塊的 [ **伺服器名稱** ] 欄位中，輸入您的伺服器名稱。

3. 在 [ **驗證** ] 欄位中，選取 [ **具有 MFA 支援的 Active Directory-通用** ] (您也可以使用其他兩種 Active Directory 驗證類型，請參閱使用 [SQL) 設定和管理 Azure AD 驗證](../azure-sql/database/authentication-aad-configure.md) 。

4. 在 [ **使用者名稱** ] 欄位中，輸入您設為伺服器系統管理員 Azure AD 帳戶的名稱，例如 testuser@xxxonline.com 。

5. 選取 **[連線]** 並完成登入程式。

6. 在 [物件總管]  中，展開 [資料庫]  [系統資料庫] ->  資料夾。

7. 在 [master]  資料庫上按一下滑鼠右鍵，然後選取 [新增查詢]  。

8. 在查詢視窗中，輸入下列 T-sql 命令，然後在工具列上選取 [ **執行** ]。

   ```sql
   CREATE USER [SSISIrGroup] FROM EXTERNAL PROVIDER
   ```

   命令應該會順利完成，建立代表群組的內含使用者。

9. 清除查詢視窗，輸入下列 T-SQL 命令，然後在工具列中選取 [執行]  。

   ```sql
   ALTER ROLE dbmanager ADD MEMBER [SSISIrGroup]
   ```

   命令應該會順利完成，將建立資料庫 (SSISDB) 的能力授與內含的使用者。

10. 如果您的 SSISDB 是使用 SQL 驗證建立的，而您想要切換為使用 Azure AD 驗證，讓您的 Azure-SSIS IR 存取它，請先確定將許可權授與 **master** 資料庫的步驟已順利完成。 然後，以滑鼠右鍵按一下 **SSISDB** 資料庫，然後選取 [追加 **查詢** ]。

11. 在查詢視窗中，輸入下列 T-sql 命令，然後在工具列上選取 [ **執行** ]。

    ```sql
    CREATE USER [SSISIrGroup] FROM EXTERNAL PROVIDER
    ```

    命令應該會順利完成，建立代表群組的內含使用者。

12. 清除查詢視窗，輸入下列 T-SQL 命令，然後在工具列中選取 [執行]  。

    ```sql
    ALTER ROLE db_owner ADD MEMBER [SSISIrGroup]
    ```

    命令應該會順利完成，將存取 SSISDB 的能力授與內含的使用者。

## <a name="enable-azure-ad-on-sql-managed-instance"></a>啟用 SQL 受控執行個體上的 Azure AD

SQL 受控執行個體支援直接使用您 ADF 的受控識別來建立資料庫。 您不需要將 ADF 的受控識別加入 Azure AD 群組，也不會在 SQL 受控執行個體中建立代表該群組的內含使用者。

### <a name="configure-azure-ad-authentication-for-azure-sql-managed-instance"></a>設定 Azure SQL 受控執行個體的 Azure AD authentication

遵循 [為 SQL 受控執行個體布建 Azure Active Directory 系統管理員](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance)中的步驟。

### <a name="add-the-managed-identity-for-your-adf-as-a-user-in-sql-managed-instance"></a>將您 ADF 的受控識別新增為 SQL 受控執行個體中的使用者

在此後續步驟中，您需要 [Microsoft SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS)。

1.  啟動 SSMS。

2.  使用 **系統管理員（sysadmin** ） SQL Server 帳戶連接到 SQL 受控執行個體。 這是暫時的限制，一旦 Azure AD 的伺服器主體 (Azure SQL 受控執行個體的登入) 會成為 GA，將會移除此限制。 如果您嘗試使用 Azure AD 系統管理員帳戶來建立登入，則會看到下列錯誤：訊息15247、層級16、狀態1、行1使用者沒有執行此動作的許可權。

3.  在 [物件總管]  中，展開 [資料庫]  [系統資料庫] ->  資料夾。

4.  在 [master]  資料庫上按一下滑鼠右鍵，然後選取 [新增查詢]  。

5.  在查詢視窗中，執行下列 T-sql 腳本，以使用者身分新增您 ADF 的受控識別

    ```sql
    CREATE LOGIN [{your ADF name}] FROM EXTERNAL PROVIDER
    ALTER SERVER ROLE [dbcreator] ADD MEMBER [{your ADF name}]
    ALTER SERVER ROLE [securityadmin] ADD MEMBER [{your ADF name}]
    ```
    
    命令應該會順利完成，將建立資料庫 (SSISDB) 的能力授與 ADF 的受控識別。

6.  如果您的 SSISDB 是使用 SQL 驗證建立的，而您想要切換為使用 Azure AD 驗證，讓您的 Azure-SSIS IR 存取它，請先確定將許可權授與 **master** 資料庫的步驟已順利完成。 然後，以滑鼠右鍵按一下 **SSISDB** 資料庫，然後選取 [追加 **查詢** ]。

7.  在查詢視窗中，輸入下列 T-sql 命令，然後在工具列上選取 [ **執行** ]。

    ```sql
    CREATE USER [{your ADF name}] FOR LOGIN [{your ADF name}] WITH DEFAULT_SCHEMA = dbo
    ALTER ROLE db_owner ADD MEMBER [{your ADF name}]
    ```

    命令應該會順利完成，將存取 SSISDB 的能力授與 ADF 的受控識別。

## <a name="provision-azure-ssis-ir-in-azure-portaladf-app"></a>在 Azure 入口網站/ADF 應用程式中，佈建 Azure-SSIS IR

當您在 Azure 入口網站/ADF 應用程式中佈建 Azure-SSIS IR 時，請在 [SQL 設定]  頁面上，選取 [對您的 ADF 使用 AAD 驗證搭配受控識別]  選項。 下列螢幕擷取畫面顯示 SQL Database 裝載 SSISDB 之 IR 的設定。 針對具有 SQL 受控執行個體裝載 SSISDB 的 IR， **目錄資料庫服務層級** 和 **允許 Azure 服務存取** 設定不適用，而其他設定則相同。

如需關於如何建立 Azure-SSIS IR 的詳細資訊，請參閱[在 Azure Data Factory 中建立 Azure-SSIS 整合執行階段](./create-azure-ssis-integration-runtime.md)。

![Azure-SSIS 整合執行階段的設定](media/enable-aad-authentication-azure-ssis-ir/enable-aad-authentication.png)

## <a name="provision-azure-ssis-ir-with-powershell"></a>使用 PowerShell 佈建 Azure-SSIS IR

若要使用 PowerShell 佈建 Azure-SSIS IR，請執行下列作業：

1.  安裝 [Azure PowerShell](https://github.com/Azure/azure-powershell/releases/tag/v5.5.0-March2018) 模組。

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

當您在 Azure-SSIS IR 上執行 SSIS 套件時，您可以使用受控識別驗證來連線到各種 Azure 資源。 目前，我們已在下列連線管理員中支援受控識別驗證。

- [OLE DB 連線管理員](/sql/integration-services/connection-manager/ole-db-connection-manager#managed-identities-for-azure-resources-authentication)

- [ADO.NET 連線管理員](/sql/integration-services/connection-manager/ado-net-connection-manager#managed-identities-for-azure-resources-authentication)

- [Azure 儲存體連線管理員](/sql/integration-services/connection-manager/azure-storage-connection-manager#managed-identities-for-azure-resources-authentication)
