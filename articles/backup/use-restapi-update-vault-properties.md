---
title: 使用 REST API 更新復原服務保存庫設定
description: 在本文中，您將瞭解如何使用 REST API 來更新保存庫的設定。
ms.topic: conceptual
ms.date: 12/06/2019
ms.assetid: 9aafa5a0-1e57-4644-bf79-97124db27aa2
ms.openlocfilehash: 6cecbb18e0cd6f548e1688ef978f10dcee7d9fbc
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79252359"
---
# <a name="update-azure-recovery-services-vault-configurations-using-rest-api"></a>使用 REST API 更新 Azure 復原服務保存庫設定

本文說明如何使用 REST API 來更新 Azure 復原服務保存庫中的備份相關設定。

## <a name="soft-delete-state"></a>虛刪除狀態

刪除受保護專案的備份是必須監視的重要作業。 為了防止意外刪除，Azure 復原服務保存庫具有虛刪除功能。 這項功能可讓客戶在刪除後的一段時間內，視需要還原已刪除的備份。

但在某些情況下，這項功能並不是必要的。 如果 Azure 復原服務保存庫中有備份專案，甚至虛刪除了它，就無法刪除。 如果需要立即刪除保存庫，這可能會造成問題。 例如：部署作業通常會在同一個工作流程中清除已建立的資源。 部署可以建立保存庫、設定專案的備份、執行測試還原，然後繼續刪除備份專案和保存庫。 如果保存庫刪除失敗，則整個部署可能會失敗。 停用虛刪除是保證立即刪除的唯一方法。

因此，客戶必須謹慎地根據案例，選擇是否要停用特定保存庫的虛刪除。 如需詳細資訊，請參閱虛[刪除文章](backup-azure-security-feature-cloud.md#soft-delete)。

### <a name="fetch-soft-delete-state-using-rest-api"></a>使用 REST API 提取虛刪除狀態

根據預設，會針對任何新建立的復原服務保存庫啟用虛刪除狀態。 若要提取/更新保存庫的虛刪除狀態，請使用備份保存庫的設定相關[REST API 檔](https://docs.microsoft.com/rest/api/backup/backupresourcevaultconfigs)

若要提取保存庫之虛刪除的目前狀態，請使用下列*GET*作業

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupconfig/vaultconfig?api-version=2019-05-13
```

GET URI 具有 `{subscriptionId}`、`{vaultName}``{vaultresourceGroupName}` 參數。 在此範例中，`{vaultName}` 為 "testVault"，`{vaultresourceGroupName}` 為 "testVaultRG"。 因為已在 URI 中指定所有必要參數，所以不需要個別的要求本文。

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupconfig/vaultconfig?api-version=2019-05-13
```

#### <a name="responses"></a>回應

「取得」作業的成功回應如下所示：

|名稱  |類型  |描述  |
|---------|---------|---------|
|200 確定     |   [BackupResourceVaultConfig](https://docs.microsoft.com/rest/api/backup/backupresourcevaultconfigs/get#backupresourcevaultconfigresource)      | [確定]        |

##### <a name="example-response"></a>範例回應

提交「GET」要求之後，就會傳回200（成功）回應。

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testvaultRG/providers/Microsoft.RecoveryServices/vaults/testvault/backupconfig/vaultconfig",
  "name": "vaultconfig",
  "type": "Microsoft.RecoveryServices/vaults/backupconfig",
  "properties": {
    "enhancedSecurityState": "Enabled",
    "softDeleteFeatureState": "Enabled"
  }
}
```

### <a name="update-soft-delete-state-using-rest-api"></a>使用 REST API 更新虛刪除狀態

若要使用 REST API 來更新復原服務保存庫的虛刪除狀態，請使用下列*修補*作業

```http
PATCH https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupconfig/vaultconfig?api-version=2019-05-13
```

PATCH URI 有 `{subscriptionId}`、`{vaultName}``{vaultresourceGroupName}` 參數。 在此範例中，`{vaultName}` 為 "testVault"，`{vaultresourceGroupName}` 為 "testVaultRG"。 如果我們將 URI 取代為上述的值，則 URI 看起來會像這樣。

```http
PATCH https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupconfig/vaultconfig?api-version=2019-05-13
```

#### <a name="create-the-request-body"></a>建立要求本文

下列一般定義是用來建立要求本文

如需詳細資訊，請參閱[REST API 檔](https://docs.microsoft.com/rest/api/backup/backupresourcevaultconfigs/update#request-body)

|名稱  |必要  |類型  |描述  |
|---------|---------|---------|---------|
|etag     |         |   String      |  選擇性 eTag       |
|location     |  true       |String         |   資源位置      |
|properties     |         | [VaultProperties](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#vaultproperties)        |  保存庫的屬性       |
|tags     |         | Object        |     資源標籤    |

#### <a name="example-request-body"></a>要求本文範例

下列範例是用來將虛刪除狀態更新為「已停用」。

```json
{
  "properties": {
    "enhancedSecurityState": "Enabled",
    "softDeleteFeatureState": "Disabled"
  }
}
```

#### <a name="responses"></a>回應

「修補程式」作業的成功回應如下所示：

|名稱  |類型  |描述  |
|---------|---------|---------|
|200 確定     |   [BackupResourceVaultConfig](https://docs.microsoft.com/rest/api/backup/backupresourcevaultconfigs/get#backupresourcevaultconfigresource)      | [確定]        |

##### <a name="example-response"></a>範例回應

提交「PATCH」要求之後，就會傳回200（成功）回應。

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testvaultRG/providers/Microsoft.RecoveryServices/vaults/testvault/backupconfig/vaultconfig",
  "name": "vaultconfig",
  "type": "Microsoft.RecoveryServices/vaults/backupconfig",
  "properties": {
    "enhancedSecurityState": "Enabled",
    "softDeleteFeatureState": "Disabled"
  }
}
```

## <a name="next-steps"></a>後續步驟

[建立備份原則以供在此保存庫中備份 Azure VM](backup-azure-arm-userestapi-createorupdatepolicy.md)。

如需 Azure REST API 的詳細資訊，請參閱下列文件：

- [Azure 復原服務提供者 REST API](/rest/api/recoveryservices/)
- [Get started with Azure REST API](/rest/api/azure/) (開始使用 Azure REST API)
