---
title: 註冊和掃描 Azure SQL Database
description: 本教學課程說明如何掃描 Azure SQL Database
author: hophan
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 10/02/2020
ms.openlocfilehash: 1fbeedd8643a777b29ebe4993eed7b664240621c
ms.sourcegitcommit: fec60094b829270387c104cc6c21257826fccc54
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/09/2020
ms.locfileid: "96920274"
---
# <a name="register-and-scan-an-azure-sql-database"></a>註冊和掃描 Azure SQL Database

本文概述如何在 Purview 中註冊 Azure SQL Database 資料來源，並對其設定掃描。

## <a name="supported-capabilities"></a>支援的功能

Azure SQL Database 資料來源支援下列功能：

- **完整和增量掃描**，以擷取 Azure SQL Database 中的中繼資料和分類。

- ADF 複製和資料流程活動的資料資產之間的 **譜系**。

### <a name="known-limitations"></a>已知限制

Azure Purview 不支援掃描 Azure SQL Database 中的[檢視](https://docs.microsoft.com/sql/relational-databases/views/views?view=sql-server-ver15)。 

## <a name="prerequisites"></a>必要條件

1. 建立新的 Purview 帳戶 (如果您還沒有帳戶的話)。

1. Purview 帳戶與 Azure SQL Database 之間的網路存取。


### <a name="set-up-authentication-for-a-scan"></a>設定掃描的驗證

掃描 Azure SQL Database 的驗證。 如果您需要建立新的驗證，則必須[授與對 SQL Database 的資料庫存取權](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage)。 Purview 目前支援三種驗證方法：

- SQL 驗證
- 服務主體
- 受控識別

#### <a name="sql-authentication"></a>SQL 驗證

> [!Note]
> 只有 master 資料庫中的伺服器層級主體登入 (由佈建程序所建立) 或 `loginmanager` 資料庫角色成員，才能建立新登入。 授與權限之後大約需要 **15 分鐘**，Purview 帳戶才會有適當的權限可掃描資源。

您可以依照[建立登入](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-current&preserve-view=true#examples-1)中的指示，建立 Azure SQL Database 的登入 (如果您沒有可用的登入)。 您在後續步驟將需要 **使用者名稱** 和 **密碼**。

1. 在 Azure 入口網站中瀏覽至您的金鑰保存庫
1. 選取 [設定] > [秘密]
1. 選取 [+ 產生/匯入]，然後輸入 [名稱] 以及與 Azure SQL Database 的 [密碼] 相同的 [值]
1. 選取 [建立] 以完成作業
1. 如果您的金鑰保存庫尚未連線至 Purview，您將需要[建立新的金鑰保存庫連線](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)
1. 最後，使用 **使用者名稱** 和 **密碼**[建立新的認證](manage-credentials.md#create-a-new-credential)，以設定您的掃描

#### <a name="service-principal-and-managed-identity"></a>服務主體和受控識別

有幾個步驟可讓 Purview 使用服務主體或 Purview 的 **受控識別** 掃描您的 Azure SQL Database

   > [!Note]
   > Purview 將需要 **應用程式 (用戶端) 識別碼** 和 **用戶端密碼** 才能進行掃描。

##### <a name="create-or-use-an-existing-service-principal"></a>建立或使用現有的服務主體

> [!Note]
> 如果您使用 Purview 的 **受控識別**，請略過此步驟

若要使用服務主體，您可以使用現有的主題或建立新的。 

> [!Note]
> 如果需要建立新的服務主體，請依照下列步驟操作：
> 1. 瀏覽至 [Azure 入口網站](https://portal.azure.com)。
> 1. 從左側功能表中，選取 [Azure Active Directory]。
> 1. 選取 **應用程式註冊**。
> 1. 選取 [+新增應用程式註冊]。
> 1. 輸入 **應用程式** 的名稱 (服務主體名稱)。
> 1. 選取 [僅此組織目錄中的帳戶]。
> 1. 在 [重新導向 URI] 中選取 [Web]，並輸入您想要的任何 URL；不一定要實際或工作。
> 1. 然後，選取 [註冊]。

##### <a name="configure-azure-ad-authentication-in-the-database-account"></a>在資料庫帳戶中設定 Azure AD 驗證

服務主體或受控識別必須有權取得資料庫、結構描述和資料表的中繼資料。 此外也必須能夠查詢要取樣以進行分類的資料表。

- [使用 Azure SQL 設定和管理 Azure AD 驗證](https://docs.microsoft.com/azure/azure-sql/database/authentication-aad-configure)
- 如果您使用受控識別，則 Purview 帳戶會有本身的受控識別，這基本上是您在建立 Purview 時的 Purview 名稱。 您必須依照[在 Azure SQL Database 中建立服務主體使用者](https://docs.microsoft.com/azure/azure-sql/database/authentication-aad-service-principal-tutorial#create-the-service-principal-user-in-azure-sql-database)的教學課程，在 Azure SQL Database 中使用確切的 Purview 受控識別或您本身的服務主體建立 Azure AD 使用者。 您必須將 `db_owner` (**建議**) 權限指派給身分識別。 建立使用者並授與權限的範例 SQL 語法：

    ```sql
    CREATE USER [Username] FROM EXTERNAL PROVIDER
    GO
    
    EXEC sp_addrolemember 'db_owner', [Username]
    GO
    ```

    > [!Note]
    > `Username` 是您本身的服務主體或 Purview 的受控識別
    
##### <a name="add-service-principal-to-key-vault-and-purviews-credential"></a>將服務主體新增至金鑰保存庫和 Purview 的認證

> [!Note]
> 如果您打算使用 Purview 的 **受控識別**，您可以略過此步驟，因為預設 Purview 的受控識別已在 **Purview-MSI** 認證中。

必須取得服務主體的應用程式識別碼和秘密：

1. 在 [Azure 入口網站](https://portal.azure.com)中瀏覽至您的服務主體
1. 從 [概觀] 複製 [應用程式 (用戶端) 識別碼] 的值，並從 [憑證和秘密] 複製 [用戶端密碼]。
1. 瀏覽至您的金鑰保存庫
1. 選取 [設定] > [秘密]
1. 選取 [+ 產生/匯入]，然後輸入您選擇的 [名稱]，以及與服務主體的 [用戶端密碼] 相同的 [值]
1. 選取 [建立] 以完成作業
1. 如果您的金鑰保存庫尚未連線至 Purview，您將需要[建立新的金鑰保存庫連線](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)
1. 最後，使用服務主體[建立新的認證](manage-credentials.md#create-a-new-credential)，以設定您的掃描

### <a name="firewall-settings"></a>防火牆設定

您的資料庫伺服器必須允許啟用 Azure 連線。 這可讓 Azure Purview 存取及連接伺服器。 您可以遵循[從 Azure 內部連線](../azure-sql/database/firewall-configure.md#connections-from-inside-azure)的操作指南。

1. 瀏覽至您的資料庫帳戶
1. 在 [概觀] 頁面中選取伺服器名稱
1. 選取 [安全性] > [防火牆與虛擬網路]
1. 在 [允許 Azure 服務和資源存取此伺服器] 上，選取 [是]

    :::image type="content" source="media/register-scan-azure-sql-database/sql-firewall.png" alt-text="允許 Azure 服務和資源存取此伺服器。" border="true":::
    
> [!Note]
> Azure Purview 目前不支援 VNET 設定。 因此，您無法進行以 IP 為基礎的防火牆設定。

## <a name="register-an-azure-sql-database-data-source"></a>註冊 Azure SQL Database 資料來源

若要在您的資料目錄中註冊新的 Azure SQL Database，請執行下列動作：

1. 瀏覽至您的 Purview 帳戶

1. 在左側導覽列上選取 [來源]

1. 選取 [註冊]

1. 在 [註冊來源] 上，選取 [Azure SQL Database]。 選取 [繼續]。

:::image type="content" source="media/register-scan-azure-sql-database/register-new-data-source.png" alt-text="註冊新的資料來源" border="true":::

在 [註冊來源 (Azure SQL Database)] 畫面上，執行下列動作：

1. 輸入會在目錄中列出的資料來源 **名稱**。
1. 選擇您要指向所需儲存體帳戶的方式：
   1. 選取 [從 Azure 訂用帳戶]、從 [Azure 訂用帳戶] 下拉式方塊中選取適當的訂用帳戶，然後從 [伺服器名稱] 下拉式方塊中選取適當的伺服器。
   1. 或者，您可以選取 [手動輸入]，並輸入 **伺服器名稱**。
1. **完成** 以註冊資料來源。

:::image type="content" source="media/register-scan-azure-sql-database/add-azure-sql-database.png" alt-text="註冊來源選項" border="true":::

[!INCLUDE [create and manage scans](includes/manage-scans.md)]

> [!NOTE]
> 刪除掃描時，並不會從先前的 Azure SQL Database 掃描中刪除您的資產。

## <a name="next-steps"></a>後續步驟

- [瀏覽 Azure Purview 資料目錄](how-to-browse-catalog.md)
- [搜尋 Azure Purview 資料目錄](how-to-search-catalog.md)
