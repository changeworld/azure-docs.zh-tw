---
title: 註冊和掃描 Azure SQL Database 受控執行個體
description: 本教學課程說明如何掃描 Azure SQL Database 受控執行個體
author: hophanms
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 12/01/2020
ms.openlocfilehash: 2391a36c02e0bdfe0ea98be7f19fc64f1fbb676f
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98879927"
---
# <a name="register-and-scan-an-azure-sql-database-managed-instance"></a>註冊和掃描 Azure SQL Database 受控執行個體

本文概述如何在 Purview 中註冊 Azure SQL Database 受控執行個體資料來源，並對其設定掃描。

## <a name="supported-capabilities"></a>支援的功能

Azure SQL Database 受控執行個體資料來源支援下列功能：

- **完整和增量掃描**，以擷取 Azure SQL Database 受控執行個體中的中繼資料和分類。

- ADF 複製和資料流程活動的資料資產之間的 **譜系**。

### <a name="known-limitations"></a>已知限制

Azure Purview 不支援掃描 Azure SQL 受控執行個體中的[檢視](/sql/relational-databases/views/views?view=azuresqldb-mi-current&preserve-view=true)。

## <a name="prerequisites"></a>必要條件

- 建立新的 Purview 帳戶 (如果您還沒有帳戶的話)。

- [在 Azure SQL 受控執行個體中設定公用端點](../azure-sql/managed-instance/public-endpoint-configure.md)
    > [!Note]
    > 您的組織必須能夠允許公用端點，因為 Purview **尚不支援私人端點**。 如果您使用私人端點，掃描將不會成功。

### <a name="setting-up-authentication-for-a-scan"></a>設定掃描的驗證

掃描 Azure SQL Database 受控執行個體的驗證。 如果您需要建立新的驗證，則必須[授與對 SQL Database 受控執行個體的資料庫存取權](../azure-sql/database/logins-create-manage.md)。 Purview 目前支援三種驗證方法：

- SQL 驗證
- 服務主體
- 受控識別

#### <a name="sql-authentication"></a>SQL 驗證

> [!Note]
> 只有 master 資料庫中的伺服器層級主體登入 (由佈建程序所建立) 或 `loginmanager` 資料庫角色成員，才能建立新登入。 授與權限之後大約需要 **15 分鐘**，Purview 帳戶才會有適當的權限可掃描資源。

您可以依照[建立登入](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-current&preserve-view=true#examples-1)中的指示，建立 Azure SQL Database 受控執行個體的登入 (如果您沒有可用的登入)。 您在後續步驟將需要 **使用者名稱** 和 **密碼**。

1. 在 Azure 入口網站中瀏覽至您的金鑰保存庫
1. 選取 [設定] > [秘密]
1. 選取 [+ 產生/匯入]，然後輸入 [名稱] 以及與 Azure SQL Database 受控執行個體的 [密碼] 相同的 [值]
1. 選取 [建立] 以完成作業
1. 如果您的金鑰保存庫尚未連線至 Purview，您將需要[建立新的金鑰保存庫連線](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)
1. 最後，使用 **使用者名稱** 和 **密碼**[建立新的認證](manage-credentials.md#create-a-new-credential)，以設定您的掃描

#### <a name="service-principal-and-managed-identity"></a>服務主體和受控識別

有幾個步驟可讓 Purview 使用服務主體掃描您的 Azure SQL Database 受控執行個體

##### <a name="create-or-use-an-existing-service-principal"></a>建立或使用現有的服務主體

> [!Note]
> 如果您使用 **受控識別**，請略過此步驟

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
- [使用 Azure SQL 設定和管理 Azure AD 驗證](../azure-sql/database/authentication-aad-configure.md)
- 依照[建立對應至 Azure AD 身分識別的自主使用者](../azure-sql/database/authentication-aad-configure.md?tabs=azure-powershell#create-contained-users-mapped-to-azure-ad-identities)的必要條件和教學課程，在 Azure SQL Database 受控執行個體中建立 Azure AD 使用者
- 將 `db_owner` (**建議**) 權限指派給身分識別

##### <a name="add-service-principal-to-key-vault-and-purviews-credential"></a>將服務主體新增至金鑰保存庫和 Purview 的認證

> [!Note]
> 如果您打算使用 **受控識別**，您可以略過此步驟，因為預設的 Purview 身分識別已在 **Purview-MSI** 中

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

    :::image type="content" source="media/register-scan-azure-sql-database/sql-firewall.png" alt-text="註冊來源選項" border="true":::
    
> [!Note]
> Azure Purview 目前不支援 VNET 設定。 因此，您無法進行以 IP 為基礎的防火牆設定。

## <a name="register-an-azure-sql-database-managed-instance-data-source"></a>註冊 Azure SQL Database 受控執行個體資料來源

1. 瀏覽至您的 Purview 帳戶

1. 在左側導覽列上選取 [來源]

1. 選取 [註冊]

1. 選取 [Azure SQL Database 受控執行個體]，然後選取 [繼續]

    :::image type="content" source="media/register-scan-azure-sql-database-managed-instance/set-up-the-sql-data-source.png" alt-text="設定 SQL 資料來源":::

1. 選取 [手動輸入]

1. 提供 **公用端點完整網域名稱** 和 **連接埠號碼**。 然後，選取 [完成] 以註冊資料來源。

    :::image type="content" source="media/register-scan-azure-sql-database-managed-instance/add-azure-sql-database-managed-instance.png" alt-text="Azure SQL Database 受控執行個體":::

    例如 `foobar.public.123.database.windows.net,3342`

[!INCLUDE [create and manage scans](includes/manage-scans.md)]

> [!NOTE]
> 刪除掃描時，並不會從先前的 Azure SQL Database 受控執行個體掃描中刪除您的資產。

## <a name="next-steps"></a>後續步驟

- [瀏覽 Azure Purview 資料目錄](how-to-browse-catalog.md)
- [搜尋 Azure Purview 資料目錄](how-to-search-catalog.md)