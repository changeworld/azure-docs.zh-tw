---
title: Azure Cosmos DB 中的角色型存取控制
description: 瞭解 Azure Cosmos DB 如何通過活動目錄集成 （RBAC） 提供資料庫保護。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: mjbrown
ms.openlocfilehash: 0c7332a42751b35b6ad8ec3f88afb7bc78cc85e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75445092"
---
# <a name="role-based-access-control-in-azure-cosmos-db"></a>Azure Cosmos DB 中的角色型存取控制

Azure Cosmos DB 為 Azure Cosmos DB 中的常見管理方案提供了基於角色的內置存取控制 （RBAC）。 在 Azure 活動目錄中具有設定檔的個人可以將這些 RBAC 角色指派給使用者、組、服務主體或託管標識，以授予或拒絕對 Azure Cosmos 資料庫資源上的資源和操作的存取權限。 角色指派範圍僅限於控制平面訪問，包括訪問 Azure Cosmos 帳戶、資料庫、容器和產品/服務（輸送量）。

## <a name="built-in-roles"></a>內建角色

以下是 Azure Cosmos DB 支援的內置角色：

|**內建角色**  |**描述**  |
|---------|---------|
|[DocumentDB 帳戶參與者](../role-based-access-control/built-in-roles.md#documentdb-account-contributor)|可以管理 Azure Cosmos DB 帳戶。|
|[宇宙資料庫帳戶讀取器](../role-based-access-control/built-in-roles.md#cosmos-db-account-reader-role)|可以讀取 Azure Cosmos DB 帳戶資料。|
|[宇宙備份運算子](../role-based-access-control/built-in-roles.md#cosmosbackupoperator)|可以提交 Azure Cosmos 資料庫或容器的還原請求。|
|[宇宙資料庫運算子](../role-based-access-control/built-in-roles.md#cosmos-db-operator)|可以預配 Azure Cosmos 帳戶、資料庫和容器，但不能訪問訪問資料所需的金鑰。|

> [!IMPORTANT]
> Azure Cosmos DB 中的 RBAC 支援僅適用于控制平面操作。 使用主鍵或資源權杖保護資料平面操作。 要瞭解詳細資訊，請參閱[Azure Cosmos DB 中對資料的安全訪問](secure-access-to-data.md)

## <a name="identity-and-access-management-iam"></a>身份和訪問管理 （IAM）

Azure 門戶中的**存取控制 （IAM）** 窗格用於配置 Azure Cosmos 資源上基於角色的存取控制。 這些角色應用於活動目錄中的使用者、組、服務主體和託管標識。 您可以將內置角色或自訂角色用於個人和組。 以下螢幕截圖顯示了 Azure 門戶中使用存取控制 （IAM） 的活動目錄集成 （RBAC）：

![Azure 入口網站中的存取控制 (IAM) - 示範資料庫安全性](./media/role-based-access-control/database-security-identity-access-management-rbac.png)

## <a name="custom-roles"></a>自訂角色

除了內置角色外，使用者還可以在 Azure 中創建[自訂角色](../role-based-access-control/custom-roles.md)，並將這些角色應用於其 Active Directory 租戶中的所有訂閱的服務主體。 自訂角色為使用者提供了一種使用自訂資來源提供者操作集創建 RBAC 角色定義的方法。 要瞭解哪些操作可用於為 Azure Cosmos DB 構建自訂角色，請參閱[Azure Cosmos DB 資來源提供者操作](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)

## <a name="preventing-changes-from-cosmos-sdk"></a>防止來自宇宙 SDK 的更改

可以鎖定 Cosmos 資來源提供者，以防止任何用戶端通過帳戶金鑰（即通過 Cosmos SDK 連接的應用程式）對資源進行任何更改，包括 Cosmos 帳戶、資料庫、容器和輸送量。 設置後，對任何資源的更改必須來自具有適當 RBAC 角色和憑據的使用者。 此功能在 Cosmos`disableKeyBasedMetadataWriteAccess`資來源提供者中使用屬性值進行設置。 下面列出了具有此屬性設置的 Azure 資源管理器範本的示例。

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

## <a name="next-steps"></a>後續步驟

- [Azure 資源的基於角色的存取控制 （RBAC） 是什麼](../role-based-access-control/overview.md)
- [適用於 Azure 資源的自訂角色](../role-based-access-control/custom-roles.md)
- [Azure 宇宙資料庫資來源提供者操作](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)
