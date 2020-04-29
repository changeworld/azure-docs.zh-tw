---
title: 資產轉換 REST API
description: 說明如何透過 REST API 轉換資產
author: florianborn71
ms.author: flborn
ms.date: 02/04/2020
ms.topic: how-to
ms.openlocfilehash: 38116efc9e87eca8e2514a0a84045a69b8d42326
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80887039"
---
# <a name="use-the-model-conversion-rest-api"></a>使用模型轉換 REST API

[模型轉換](model-conversion.md)服務是透過[REST API](https://en.wikipedia.org/wiki/Representational_state_transfer)來控制。 本文說明轉換服務 API 的詳細資料。

## <a name="regions"></a>區域

查看基底 Url 用來傳送要求的[可用區域清單](../../reference/regions.md)。

## <a name="common-headers"></a>一般標頭

### <a name="common-request-headers"></a>常見的要求標頭

所有要求都必須指定這些標頭：

- **Authorization**標頭的值必須是 "持有人 [*token*]"，其中 [*token*] 是[服務存取權杖](../tokens.md)。

### <a name="common-response-headers"></a>常見的回應標頭

所有回應都包含下列標頭：

- **MS-CV**標頭包含唯一的字串，可用來追蹤服務內的呼叫。

## <a name="endpoints"></a>端點

轉換服務會提供三個 REST API 端點來執行下列動作：

- 使用與您的 Azure 遠端轉譯帳戶連結的儲存體帳戶，啟動模型轉換。 
- 使用提供的*共用存取簽章（SAS）* 啟動模型轉換。
- 查詢轉換狀態

### <a name="start-conversion-using-a-linked-storage-account"></a>使用連結的儲存體帳戶開始轉換
您的 Azure 遠端轉譯帳戶必須遵循如何[連結儲存體帳戶](../create-an-account.md#link-storage-accounts)中的步驟，才能存取提供的儲存體帳戶。

| 端點 | 方法 |
|-----------|:-----------|
| /v1/accounts/**accountID**/conversions/create | POST |

傳回進行中轉換的識別碼，包裝在 JSON 檔中。 功能變數名稱為 "conversionId"。

#### <a name="request-body"></a>要求本文


```json
{
    "input":
    {
        "storageAccountname": "<the name of a connected storage account - this does not include the domain suffix (.blob.core.windows.net)>",
        "blobContainerName": "<the name of the blob container containing your input asset data>",
        "folderPath": "<optional: can be omitted or empty - a subpath in the input blob container>",
        "inputAssetPath" : "<path to the model in the input blob container relative to the folderPath (or container root if no folderPath is specified)>"
    },
    "output":
    {
        "storageAccountname": "<the name of a connected storage account - this does not include the domain suffix (.blob.core.windows.net)>",
        "blobContainerName": "<the name of the blob container where the converted asset will be copied to>",
        "folderPath": "<optional: can be omitted or empty - a subpath in the output blob container. Will contain the asset and log files>",
        "outputAssetFileName": "<optional: can be omitted or empty. The filename of the converted asset. If provided the filename needs to end in .arrAsset>"
    }
}
```
### <a name="start-conversion-using-provided-shared-access-signatures"></a>使用提供的共用存取簽章開始轉換
如果您的 ARR 帳戶未連結至您的儲存體帳戶，此 REST 介面可讓您使用*共用存取簽章（SAS）* 來提供存取權。

| 端點 | 方法 |
|-----------|:-----------|
| /v1/accounts/**accountID**/conversions/createWithSharedAccessSignature | POST |

傳回進行中轉換的識別碼，包裝在 JSON 檔中。 功能變數名稱為 "conversionId"。

#### <a name="request-body"></a>要求本文

要求主體與上述的建立 REST 呼叫中的相同，但輸入和輸出包含*共用存取簽章（SAS）權杖*。 這些權杖可讓您存取儲存體帳戶以讀取輸入和寫入轉換結果。

> [!NOTE]
> 這些 SAS URI 權杖是查詢字串，而不是完整的 URI。 


```json
{
    "input":
    {
        "storageAccountname": "<the name of a connected storage account - this does not include the domain suffix (.blob.core.windows.net)>",
        "blobContainerName": "<the name of the blob container containing your input asset data>",
        "folderPath": "<optional: can be omitted or empty - a subpath in the input blob container>",
        "inputAssetPath" : "<path to the model in the input blob container relative to the folderPath (or container root if no folderPath is specified)>",
        "containerReadListSas" : "<a container SAS token which gives read and list access to the given input blob container>"
    },
    "output":
    {
        "storageAccountname": "<the name of a connected storage account - this does not include the domain suffix (.blob.core.windows.net)>",
        "blobContainerName": "<the name of the blob container where the converted asset will be copied to>",
        "folderPath": "<optional: can be omitted or empty - a subpath in the output blob container. Will contain the asset and log files>",
        "outputAssetFileName": "<optional: can be omitted or empty. The filename of the converted asset. If provided the filename needs to end in .arrAsset>",
        "containerWriteSas" : "<a container SAS token which gives write access to the given output blob container>"
    }
}
```

### <a name="poll-conversion-status"></a>輪詢轉換狀態
使用上述其中一個 REST 呼叫來開始進行中的轉換狀態，可以使用下列介面來進行查詢：


| 端點 | 方法 |
|-----------|:-----------|
| /v1/accounts/**accountID**/conversions/**conversionId** | GET |

傳回具有 "status" 欄位且具有下列值的 JSON 檔：

- 運行
- "Success"
- 出

如果狀態為「失敗」，則會有一個額外的「錯誤」欄位，內含含有錯誤資訊的「訊息」子欄位。 其他記錄將會上傳至您的輸出容器。

## <a name="next-steps"></a>後續步驟

- [使用 Azure Blob 儲存體進行模型轉換](blob-storage.md)
- [模型轉換](model-conversion.md)
