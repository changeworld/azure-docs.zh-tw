---
title: 使用 REST API 建立復原服務保存庫
description: 在本文中，您將瞭解如何使用 REST API 來管理 Azure VM 備份的備份和還原作業。
ms.topic: conceptual
ms.date: 08/21/2018
ms.assetid: e54750b4-4518-4262-8f23-ca2f0c7c0439
ms.openlocfilehash: 1901c35d2b4d8bcd02cc064fcfc844e19969e3b5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "74173406"
---
# <a name="create-azure-recovery-services-vault-using-rest-api"></a>使用 REST API 建立 Azure 復原服務保存庫

[建立保存庫 REST API](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate) 文件概述使用 REST API 建立 Azure 復原服務保存庫的步驟。 讓我們使用這份文件作為參考，以在美國西部建立稱為 "testVault" 的保存庫。

若要建立或更新 Azure 復原服務保存庫，請使用下列 *PUT* 作業。

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}?api-version=2016-06-01
```

## <a name="create-a-request"></a>建立要求

若要建立 *PUT* 要求，`{subscription-id}` 是必要參數。 如果您有多個訂用帳戶，請參閱使用[多個訂閱](/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest)。 您需針對資源定義 `{resourceGroupName}` 和 `{vaultName}`，以及定義 `api-version` 參數。 本文使用 `api-version=2016-06-01`。

以下是必要標頭：

| 要求標頭   | 描述 |
|------------------|-----------------|
| *Content-Type:*  | 必要。 設定為 `application/json`。 |
| *Authorization:* | 必要。 設定為有效的 `Bearer` [存取權杖](https://docs.microsoft.com/rest/api/azure/#authorization-code-grant-interactive-clients)。 |

如需如何建立要求的詳細資訊，請參閱 [REST API 要求/回應的元件](/rest/api/azure/#components-of-a-rest-api-requestresponse)。

## <a name="create-the-request-body"></a>建立要求本文

以下是用來建立要求本文的常用定義：

|名稱  |必要  |類型  |說明  |
|---------|---------|---------|---------|
|etag     |         |   String      |  選擇性 eTag       |
|location     |  true       |String         |   資源位置      |
|properties     |         | [VaultProperties](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#vaultproperties)        |  保存庫的屬性       |
|sku     |         |  [限量](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#sku)       |    識別每個 Azure 資源的唯一系統識別碼     |
|tags     |         | Object        |     資源標籤    |

請注意，保存庫名稱和資源群組名稱會在 PUT URI 中提供。 要求本文會定義位置。

## <a name="example-request-body"></a>要求本文範例

下列範例本文用於在美國西部建立保存庫。 指定位置。 SKU 一律是「標準」。

```json
{
  "properties": {},
  "sku": {
    "name": "Standard"
  },
  "location": "West US"
}
```

## <a name="responses"></a>回應

建立或更新復原服務保存庫的作業會有兩個成功的回應：

|名稱  |類型  |說明  |
|---------|---------|---------|
|200 確定     |   [保險櫃](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#vault)      | [確定]        |
|201 Created     | [保險櫃](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#vault)        |   建立時間      |

如需 REST API 回應的詳細資訊，請參閱[處理回應訊息](/rest/api/azure/#process-the-response-message)。

### <a name="example-response"></a>範例回應

先前範例要求本文所傳回的扼要 *201 Created* 回應會顯示已指派 *id*，且 *provisioningState* 為 *Succeeded*：

```json
{
  "location": "westus",
  "name": "testVault",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "id": "/subscriptions/77777777-b0c6-47a2-b37c-d8e65a629c18/resourceGroups/Default-RecoveryServices-ResourceGroup/providers/Microsoft.RecoveryServices/vaults/testVault",
  "type": "Microsoft.RecoveryServices/vaults",
  "sku": {
    "name": "Standard"
  }
}
```

## <a name="next-steps"></a>後續步驟

[建立備份原則以供在此保存庫中備份 Azure VM](backup-azure-arm-userestapi-createorupdatepolicy.md)。

如需 Azure REST API 的詳細資訊，請參閱下列文件：

- [Azure 復原服務提供者 REST API](/rest/api/recoveryservices/)
- [Get started with Azure REST API](/rest/api/azure/) (開始使用 Azure REST API)
