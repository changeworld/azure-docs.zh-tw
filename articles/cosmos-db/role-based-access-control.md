---
title: Azure Cosmos DB 中的角色型存取控制
description: 瞭解 Azure Cosmos DB 如何透過 Active directory 整合（RBAC）提供資料庫保護。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: mjbrown
ms.openlocfilehash: 4e028e7a5e7e7b8f747d7a1cfb36c553a8113544
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "82583728"
---
# <a name="role-based-access-control-in-azure-cosmos-db"></a>Azure Cosmos DB 中的角色型存取控制

Azure Cosmos DB 提供內建的角色型存取控制（RBAC），適用于 Azure Cosmos DB 中的常見管理案例。 在 Azure Active Directory 中具有設定檔的個人可以將這些 RBAC 角色指派給使用者、群組、服務主體或受控識別，以授與或拒絕對 Azure Cosmos DB 資源的資源和作業的存取權。 角色指派的範圍僅限於控制平面存取，其中包括 Azure Cosmos 帳戶、資料庫、容器和供應專案（輸送量）的存取權。

## <a name="built-in-roles"></a>內建角色

以下是 Azure Cosmos DB 支援的內建角色：

|**內建角色**  |**說明**  |
|---------|---------|
|[DocumentDB 帳戶參與者](../role-based-access-control/built-in-roles.md#documentdb-account-contributor)|可以管理 Azure Cosmos DB 帳戶。|
|[Cosmos DB 帳戶讀者](../role-based-access-control/built-in-roles.md#cosmos-db-account-reader-role)|可以讀取 Azure Cosmos DB 帳戶資料。|
|[Cosmos 備份操作員](../role-based-access-control/built-in-roles.md#cosmosbackupoperator)|可以提交 Azure Cosmos 資料庫或容器的還原要求。|
|[Cosmos DB 運算子](../role-based-access-control/built-in-roles.md#cosmos-db-operator)|可以布建 Azure Cosmos 帳戶、資料庫和容器，但無法存取存取資料所需的金鑰。|

> [!IMPORTANT]
> Azure Cosmos DB 中的 RBAC 支援僅適用于控制平面作業。 使用主要金鑰或資源權杖來保護資料平面作業。 若要深入瞭解，請參閱[安全存取 Azure Cosmos DB 中的資料](secure-access-to-data.md)

## <a name="identity-and-access-management-iam"></a>身分識別與存取管理（IAM）

Azure 入口網站中的 [**存取控制（IAM）** ] 窗格用來設定 Azure Cosmos 資源上的角色型存取控制。 這些角色會套用至 Active Directory 中的使用者、群組、服務主體和受控識別。 您可以針對個人和群組使用內建角色或自訂角色。 下列螢幕擷取畫面顯示使用 Azure 入口網站中的存取控制（IAM） Active Directory 整合（RBAC）：

![Azure 入口網站中的存取控制 (IAM) - 示範資料庫安全性](./media/role-based-access-control/database-security-identity-access-management-rbac.png)

## <a name="custom-roles"></a>自訂角色

除了內建角色以外，使用者也可以在 Azure 中建立[自訂角色](../role-based-access-control/custom-roles.md)，並將這些角色套用至其 Active Directory 租使用者中所有訂用帳戶的服務主體。 自訂角色可讓使用者以一組自訂的資源提供者作業來建立 RBAC 角色定義。 若要瞭解哪些作業可用來建立的自訂角色 Azure Cosmos DB 參閱[Azure Cosmos DB 資源提供者作業](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)

## <a name="preventing-changes-from-cosmos-sdk"></a>防止來自 Cosmos SDK 的變更

Cosmos 資源提供者可以鎖定，以防止任何透過帳戶金鑰（也就是透過 Cosmos SDK 連線的應用程式）連線的用戶端，包括 Cosmos 帳戶、資料庫、容器和輸送量的資源變更。 設定時，任何資源的變更都必須來自具有適當 RBAC 角色和認證的使用者。 這項功能是使用`disableKeyBasedMetadataWriteAccess` Cosmos 資源提供者中的屬性值來設定。 使用此屬性設定的 Azure Resource Manager 範本範例如下。

```json
{
    {
      "type": "Microsoft.DocumentDB/databaseAccounts",
      "name": "[variables('accountName')]",
      "apiVersion": "2019-08-01",
      "location": "[parameters('location')]",
      "kind": "GlobalDocumentDB",
      "properties": {
        "consistencyPolicy": "[variables('consistencyPolicy')[parameters('defaultConsistencyLevel')]]",
        "locations": "[variables('locations')]",
        "databaseAccountOfferType": "Standard",
        "disableKeyBasedMetadataWriteAccess": true
        }
    }
}
```
如果您匯出現有的 Resource Manager 範本，並使用這個屬性加以更新，它可以完全取代您的範本功能。 因此，如果未包含所有值，則會將它們重設為預設值。 另一種停用金鑰型中繼資料寫入權限的方式，是使用 Azure CLI，如下列命令所示：

```cli
az cosmosdb update  --name CosmosDBAccountName --resource-group ResourceGroupName  --disable-key-based-metadata-write-access true

```

## <a name="next-steps"></a>後續步驟

- [什麼是適用于 Azure 資源的角色型存取控制（RBAC）](../role-based-access-control/overview.md)
- [適用於 Azure 資源的自訂角色](../role-based-access-control/custom-roles.md)
- [Azure Cosmos DB 資源提供者作業](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)
