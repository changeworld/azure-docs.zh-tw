---
title: 自訂角色：線上 SQL 伺服器到 SQL 託管實例遷移
titleSuffix: Azure Database Migration Service
description: 瞭解如何使用 SQL Server 的自訂角色到 Azure SQL 資料庫託管實例連線遷移。
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 10/25/2019
ms.openlocfilehash: e9a1024ca3ab68841474ab051c029042df4915b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78254934"
---
# <a name="custom-roles-for-sql-server-to-sql-database-managed-instance-online-migrations"></a>SQL Server 到 SQL 資料庫託管實例連線遷移的自訂角色

Azure 資料庫移轉服務使用 APP ID 與 Azure 服務進行交互。 APP ID 需要訂閱級別的"參與者"角色（許多公司安全部門不允許），或者創建授予 Azure 資料庫移轉服務所需的特定許可權的自訂角色。 由於 Azure Active Directory 中有 2，000 個自訂角色的限制，因此您可能希望將 APP ID 專用所需的擁有權限合併到一個或兩個自訂角色中，然後授予 APP ID 特定物件或資源組的自訂角色（與在訂閱級別）。 如果自訂角色的數量不是問題，則可以按資源類型拆分自訂角色，以便總共創建三個自訂角色，如下所述。

角色定義 json 字串的"可分配範圍"部分允許您控制許可權在門戶中的 **"添加角色指派**"UI 中的顯示位置。 您可能希望在資源組甚至資源級別定義角色，以避免使 UI 與額外角色雜亂無章。 請注意，這不會執行實際的角色指派。

## <a name="minimum-number-of-roles"></a>最小角色數

我們目前建議為 APP ID 創建至少兩個自訂角色，一個在資源級別，另一個在訂閱級別。

> [!NOTE]
> 隨著新的 SQL 資料庫託管實例代碼部署到 Azure，最終可能會刪除最後一個自訂角色要求。

**APP ID 的自訂角色**。 在*資源*或*資源組*級別進行 Azure 資料庫移轉服務遷移需要此角色（有關 APP ID 的詳細資訊，請參閱[文章"使用門戶創建可以訪問資源的 Azure AD 應用程式和服務主體](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)"。

```json
{
  "Name": "DMS Role - App ID",
  "IsCustom": true,
  "Description": "DMS App ID access to complete MI migrations",
  "Actions": [
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.Storage/storageAccounts/listKeys/action",
        "Microsoft.Storage/storageaccounts/blobservices/read",
        "Microsoft.Storage/storageaccounts/blobservices/write",
        "Microsoft.Sql/managedInstances/read",
        "Microsoft.Sql/managedInstances/write",
        "Microsoft.Sql/managedInstances/databases/read",
        "Microsoft.Sql/managedInstances/databases/write",
        "Microsoft.Sql/managedInstances/databases/delete",
        "Microsoft.Sql/managedInstances/metrics/read",
        "Microsoft.DataMigration/locations/*",
        "Microsoft.DataMigration/services/*"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<subscription_id>/ResourceGroups/<StorageAccount_rg_name>",
    "/subscriptions/<subscription_id>/ResourceGroups/<ManagedInstance_rg_name>",
    "/subscriptions/<subscription_id>/ResourceGroups/<DMS_rg_name>",
  ]
}
```

**APP ID - 訂閱的自訂角色**. 在*訂閱*級別進行 Azure 資料庫移轉服務遷移需要此角色。

```json
{
  "Name": "DMS Role - App ID - Sub",
  "IsCustom": true,
  "Description": "DMS App ID access at subscription level to complete MI migrations",
  "Actions": [
        "Microsoft.Sql/locations/managedDatabaseRestoreAzureAsyncOperation/*"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<subscription_id>"
  ]
}
```

上面的 json 必須存儲在三個文字檔中，您可以使用 AzureRM、AZ PowerShell Cmdlet 或 Azure CLI 使用**New-AzureRmRole 定義 （AzureRM）** 或**新-AzRole定義 （AZ）** 創建角色。

有關詳細資訊，請參閱[Azure 資源的自訂角色](https://docs.microsoft.com/azure/role-based-access-control/custom-roles)一文。

創建這些自訂角色後，必須向使用者添加角色指派，並將 APP ID 添加到相應的資源或資源組：

* 必須將"DMS 角色 - 應用 ID"角色授予將用於遷移的 APP ID，以及存儲帳戶、Azure 資料庫移轉服務實例和 SQL 資料庫託管實例資源級別。
* 必須在訂閱級別授予 APP ID 的"DMS 角色 - 應用 ID - 子"角色（在資源或資源組中授予將失敗）。 在部署代碼更新之前，此要求是暫時的。

## <a name="expanded-number-of-roles"></a>增加的角色數量

如果 Azure 活動目錄中的自訂角色數不為問題，我們建議您總共創建三個角色。 您仍然需要"DMS 角色 - 應用 ID + 子"角色，但上面的"DMS 角色 - 應用 ID"角色按資源類型拆分為兩個不同的角色。

**SQL 資料庫託管實例的 APP ID 的自訂角色**

```json
{
  "Name": "DMS Role - App ID - SQL MI",
  "IsCustom": true,
  "Description": "DMS App ID access to complete MI migrations",
  "Actions": [
        "Microsoft.Sql/managedInstances/read",
        "Microsoft.Sql/managedInstances/write",
        "Microsoft.Sql/managedInstances/databases/read",
        "Microsoft.Sql/managedInstances/databases/write",
        "Microsoft.Sql/managedInstances/databases/delete",
        "Microsoft.Sql/managedInstances/metrics/read"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<subscription_id>/resourceGroups/<ManagedInstance_rg_name>"
  ]
}
```

**用於存儲的 APP ID 的自訂角色**

```json
{
  "Name": "DMS Role - App ID - Storage",
  "IsCustom": true,
  "Description": "DMS App ID storage access to complete MI migrations",
  "Actions": [
"Microsoft.Storage/storageAccounts/read",
        "Microsoft.Storage/storageAccounts/listKeys/action",
        "Microsoft.Storage/storageaccounts/blobservices/read",
        "Microsoft.Storage/storageaccounts/blobservices/write"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<subscription_id>/resourceGroups/<StorageAccount_rg_name>"
  ]
}
```

## <a name="role-assignment"></a>角色指派

要將角色指派給使用者/APP ID，請打開 Azure 門戶，請執行以下步驟：

1. 導航到資源組或資源（訂閱上需要授予的角色除外），轉到**Access Control**，然後滾動以查找剛剛創建的自訂角色。

2. 選擇適當的角色，選擇 APP ID，然後保存更改。

  您的 APP ID 現在顯示在**角色指派**選項卡上。

## <a name="next-steps"></a>後續步驟

* 在 Microsoft[資料庫移轉指南](https://datamigration.microsoft.com/)中查看方案的遷移指南。
