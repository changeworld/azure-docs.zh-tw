---
title: 使用 REST API 更新恢復服務保存庫配置
description: 在本文中，瞭解如何使用 REST API 更新保存庫的配置。
ms.topic: conceptual
ms.date: 12/06/2019
ms.assetid: 9aafa5a0-1e57-4644-bf79-97124db27aa2
ms.openlocfilehash: 6cecbb18e0cd6f548e1688ef978f10dcee7d9fbc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252359"
---
# <a name="update-azure-recovery-services-vault-configurations-using-rest-api"></a>使用 REST API 更新 Azure 恢復服務保存庫配置

本文介紹如何使用 REST API 更新 Azure 恢復服務保存庫中的備份相關配置。

## <a name="soft-delete-state"></a>虛刪除狀態

刪除受保護項的備份是必須監視的重要操作。 為了防止意外刪除，Azure 恢復服務保存庫具有虛刪除功能。 此功能允許客戶在刪除後的時間段內（如有必要）還原已刪除的備份。

但是，在某些情況下不需要此功能。 如果 Azure 恢復服務保存庫中存在備份項，甚至虛刪除的備份項，則無法刪除該保存庫。 如果需要立即刪除保存庫，則可能會帶來問題。 例如：部署操作通常清理同一工作流中創建的資源。 部署可以創建保存庫、為專案配置備份、執行測試還原，然後繼續刪除備份項和保存庫。 如果保存庫刪除失敗，整個部署可能會失敗。 禁用虛刪除是保證立即刪除的唯一方法。

因此，客戶需要根據方案仔細選擇是否禁用特定保存庫的虛刪除。 有關詳細資訊，請參閱[虛刪除文章](backup-azure-security-feature-cloud.md#soft-delete)。

### <a name="fetch-soft-delete-state-using-rest-api"></a>使用 REST API 獲取虛刪除狀態

預設情況下，虛刪除狀態將啟用任何新創建的恢復服務保存庫。 要提取/更新保存庫的虛刪除狀態，請使用備份保存庫的配置相關的 REST API[文檔](https://docs.microsoft.com/rest/api/backup/backupresourcevaultconfigs)

要獲取保存庫的虛刪除的目前狀態，請使用以下*GET*操作

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupconfig/vaultconfig?api-version=2019-05-13
```

GET URI`{subscriptionId}`具有`{vaultName}` `{vaultresourceGroupName}` 、 參數。 在此示例中，`{vaultName}`是"testVault"，`{vaultresourceGroupName}`是"測試VaultRG"。 因為已在 URI 中指定所有必要參數，所以不需要個別的要求本文。

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupconfig/vaultconfig?api-version=2019-05-13
```

#### <a name="responses"></a>回應

"GET"操作的成功回應如下所示：

|名稱  |類型  |描述  |
|---------|---------|---------|
|200 確定     |   [備份資源庫配置](https://docs.microsoft.com/rest/api/backup/backupresourcevaultconfigs/get#backupresourcevaultconfigresource)      | [確定]        |

##### <a name="example-response"></a>範例回應

提交"GET"請求後，將返回 200（成功）回應。

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

要使用 REST API 更新恢復服務保存庫的虛刪除狀態，請使用以下*PATCH*操作

```http
PATCH https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupconfig/vaultconfig?api-version=2019-05-13
```

PATCH URI`{subscriptionId}`具有`{vaultName}` `{vaultresourceGroupName}` 、 參數。 在此示例中，`{vaultName}`是"testVault"，`{vaultresourceGroupName}`是"測試VaultRG"。 如果我們用上述值替換 URI，則 URI 將如下所示。

```http
PATCH https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupconfig/vaultconfig?api-version=2019-05-13
```

#### <a name="create-the-request-body"></a>建立要求本文

遵循常見定義以創建請求正文

有關詳細資訊，請參閱 REST [API 文檔](https://docs.microsoft.com/rest/api/backup/backupresourcevaultconfigs/update#request-body)

|名稱  |必要  |類型  |描述  |
|---------|---------|---------|---------|
|etag     |         |   String      |  選擇性 eTag       |
|location     |  true       |String         |   資源位置      |
|properties     |         | [VaultProperties](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#vaultproperties)        |  保存庫的屬性       |
|tags     |         | Object        |     資源標籤    |

#### <a name="example-request-body"></a>要求本文範例

以下示例用於將虛刪除狀態更新為"已禁用"。

```json
{
  "properties": {
    "enhancedSecurityState": "Enabled",
    "softDeleteFeatureState": "Disabled"
  }
}
```

#### <a name="responses"></a>回應

"PATCH"操作的成功回應如下所示：

|名稱  |類型  |描述  |
|---------|---------|---------|
|200 確定     |   [備份資源庫配置](https://docs.microsoft.com/rest/api/backup/backupresourcevaultconfigs/get#backupresourcevaultconfigresource)      | [確定]        |

##### <a name="example-response"></a>範例回應

提交"PATCH"請求後，將返回 200（成功）回應。

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
