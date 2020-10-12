---
title: 自訂角色：線上 SQL Server 至 SQL 受控執行個體遷移
titleSuffix: Azure Database Migration Service
description: 瞭解如何使用自訂角色 SQL Server 至 Azure SQL 受控執行個體線上遷移。
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: conceptual
ms.date: 10/25/2019
ms.openlocfilehash: e950e34a1965e309d226c42bf4b3128cbe3bf3c5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91291498"
---
# <a name="custom-roles-for-sql-server-to-azure-sql-managed-instance-online-migrations"></a>SQL Server 至 Azure SQL 受控執行個體線上遷移的自訂角色

Azure 資料庫移轉服務會使用應用程式識別碼來與 Azure 服務互動。 應用程式識別碼需要訂用帳戶層級的「參與者」角色， (許多公司安全部門都不允許) 或建立自訂角色，以授與 Azure 資料庫移轉服務所需的特定許可權。 由於 Azure Active Directory 中有2000個自訂角色的限制，因此您可能會想要將應用程式識別碼所需的擁有權限結合為一或兩個自訂角色，然後在訂用帳戶層級 (與訂用帳戶層級上，將自訂角色授與應用程式識別碼) 。 如果不考慮自訂角色的數目，您可以依資源類型分割自訂角色，以建立三個自訂角色，如下所述。

角色定義 json 字串的 >assignablescopes 區段可讓您控制許可權在入口網站的 [ **新增角色指派** ] UI 中出現的位置。 您可能會想要在資源群組或甚至是資源層級定義角色，以避免讓 UI 雜亂的額外角色。 請注意，這不會執行實際的角色指派。

## <a name="minimum-number-of-roles"></a>角色的最小數目

我們目前建議至少為應用程式識別碼建立兩個自訂角色，一個在資源層級，另一個在訂用帳戶層級。

> [!NOTE]
> 當新的 SQL 受控執行個體程式碼部署至 Azure 時，最終可能會移除最後一個自訂角色需求。

**應用程式識別碼的自訂角色**。 需要有此角色，才能在 *資源* 或 *資源群組* 層級遷移 Azure 資料庫移轉服務 (如需有關應用程式識別碼的詳細資訊，請參閱 [使用入口網站來建立可存取資源的 Azure AD 應用程式和服務主體](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)) 。

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

**應用程式識別碼-訂用帳戶的自訂角色**。 *訂*用帳戶層級的 Azure 資料庫移轉服務遷移需要此角色。

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

上述 json 必須儲存在三個文字檔中，您可以使用 AzureRM、AZ PowerShell Cmdlet 或 Azure CLI 來建立使用 **get-azurermroledefinition (AzureRM) ** 或 **>get-azroledefinition (AZ) **的角色。

如需詳細資訊，請參閱 [Azure 自訂角色](https://docs.microsoft.com/azure/role-based-access-control/custom-roles)文章。

建立這些自訂角色之後，您必須將角色指派新增至使用者和應用程式識別碼， (s) 到適當的資源或資源群組：

* 「DMS 角色-應用程式識別碼」角色必須授與將用於遷移的應用程式識別碼，以及儲存體帳戶、Azure 資料庫移轉服務實例和 SQL 受控執行個體資源層級。
* 「DMS 角色-應用程式識別碼-子」角色必須授與訂用帳戶層級的應用程式識別碼， (授與資源或資源群組將會失敗) 。 在部署程式碼更新之前，這項需求是暫時性的。

## <a name="expanded-number-of-roles"></a>擴充的角色數目

如果您 Azure Active Directory 中的自訂角色數目不成問題，建議您總共建立三個角色。 您仍然需要「DMS 角色-應用程式識別碼-子」角色，但上述「DMS 角色-應用程式識別碼」角色會依資源類型分割成兩個不同的角色。

**適用于 SQL 受控執行個體的應用程式識別碼自訂角色**

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

若要將角色指派給使用者/應用程式識別碼，請開啟 Azure 入口網站，然後執行下列步驟：

1. 流覽至資源群組或資源 (除了需要授與訂用帳戶) 的角色之外，移至 [ **存取控制**]，然後進行滾動以尋找您剛才建立的自訂角色。

2. 選取適當的角色，選取 [應用程式識別碼]，然後儲存變更。

  您的應用程式識別碼 (s) 現在會顯示在 [ **角色指派** ] 索引標籤上。

## <a name="next-steps"></a>後續步驟

* 請參閱《 Microsoft [資料庫移轉指南》](https://datamigration.microsoft.com/)中您案例的遷移指導方針。
