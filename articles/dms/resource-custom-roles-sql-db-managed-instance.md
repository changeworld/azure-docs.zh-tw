---
title: 自訂角色：線上 SQL Server 至 SQL 受控實例的遷移
titleSuffix: Azure Database Migration Service
description: 瞭解如何使用適用于 SQL Server 的自訂角色，Azure SQL Database 受控實例線上遷移。
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "78254934"
---
# <a name="custom-roles-for-sql-server-to-sql-database-managed-instance-online-migrations"></a>SQL Server SQL Database 受控實例線上遷移的自訂角色

Azure 資料庫移轉服務會使用應用程式識別碼來與 Azure 服務互動。 「應用程式識別碼」需要訂用帳戶層級的「參與者」角色（許多公司安全性部門不允許）或建立自訂角色，以授與 Azure 資料庫移轉服務所需的特定許可權。 由於 Azure Active Directory 中有2000個自訂角色的限制，因此您可能會想要將應用程式識別碼特別需要的擁有權限結合成一或兩個自訂角色，然後將特定物件或資源群組（相對於訂用帳戶層級）上的自訂角色授與應用程式識別碼。 如果不考慮自訂角色的數目，您可以依資源類型分割自訂角色，以建立三個自訂角色，如下所述。

角色定義 json 字串的 AssignableScopes 區段可讓您控制許可權出現在入口網站中的 [**新增角色指派**] UI 中的位置。 您可能會想要在資源群組或甚至是資源層級定義角色，以避免 UI 有額外的角色。 請注意，這不會執行實際的角色指派。

## <a name="minimum-number-of-roles"></a>角色的最小數目

我們目前建議您至少為應用程式識別碼建立兩個自訂角色，一個在資源層級，另一個在訂用帳戶層級。

> [!NOTE]
> 最後一次自訂角色需求可能會被移除，因為新的 SQL Database 受控實常式序代碼會部署到 Azure。

**應用程式識別碼的自訂角色**。 在*資源*或*資源群組*層級進行 Azure 資料庫移轉服務遷移需要此角色（如需有關應用程式識別碼的詳細資訊，請參閱[使用入口網站建立可存取資源的 Azure AD 應用程式和服務主體一](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)文）。

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

**應用程式識別碼-訂用帳戶的自訂角色**。 Azure 資料庫移轉服務在*訂*用帳戶層級上進行遷移需要此角色。

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

上述 json 必須儲存在三個文字檔中，而且您可以使用 AzureRM、AZ PowerShell Cmdlet 或 Azure CLI，使用**get-azurermroledefinition （AzureRM）** 或**get-azroledefinition （AZ）** 來建立角色。

如需詳細資訊，請參閱[適用于 Azure 資源的自訂角色](https://docs.microsoft.com/azure/role-based-access-control/custom-roles)一文。

建立這些自訂角色之後，您必須將角色指派新增至適當資源或資源群組的使用者和應用程式識別碼：

* 「DMS 角色-應用程式識別碼」角色必須授與將用於遷移的應用程式識別碼，以及儲存體帳戶、Azure 資料庫移轉服務實例和 SQL Database 受控實例資源層級。
* 「DMS 角色-應用程式識別碼-子」角色必須授與「訂用帳戶」層級的「應用程式識別碼」（在資源或資源群組中授與將會失敗）。 這項需求是暫時性的，直到部署程式碼更新為止。

## <a name="expanded-number-of-roles"></a>擴充的角色數目

如果 Azure Active Directory 中的自訂角色數目不是問題，建議您總共建立三個角色。 您仍然需要「DMS 角色-應用程式識別碼-子」角色，但上述「DMS 角色-應用程式識別碼」角色會依資源類型分割成兩個不同的角色。

**SQL Database 受控實例之應用程式識別碼的自訂角色**

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

**適用于儲存體之應用程式識別碼的自訂角色**

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

若要將角色指派給使用者/應用程式識別碼，請開啟 Azure 入口網站，執行下列步驟：

1. 流覽至資源群組或資源（除了必須授與訂用帳戶的角色以外），移至 [**存取控制**]，然後按 [流覽] 以尋找您剛建立的自訂角色。

2. 選取適當的角色，選取 [應用程式識別碼]，然後儲存變更。

  您的應用程式識別碼現在會列在 [**角色指派**] 索引標籤上。

## <a name="next-steps"></a>後續步驟

* 請參閱《 Microsoft[資料庫移轉指南》](https://datamigration.microsoft.com/)中適用于您案例的遷移指引。
