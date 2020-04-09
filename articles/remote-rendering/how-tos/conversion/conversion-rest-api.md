---
title: 資產轉換 REST API
description: 描述如何透過 REST API 轉換資產
author: florianborn71
ms.author: flborn
ms.date: 02/04/2020
ms.topic: how-to
ms.openlocfilehash: 38116efc9e87eca8e2514a0a84045a69b8d42326
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2020
ms.locfileid: "80887039"
---
# <a name="use-the-model-conversion-rest-api"></a>使用模型轉換 REST API

[模型轉換](model-conversion.md)服務通過[REST API](https://en.wikipedia.org/wiki/Representational_state_transfer)進行控制。 本文介紹了轉換服務 API 詳細資訊。

## <a name="regions"></a>區域

請參考要將要求傳送到的基本網址[的區域清單](../../reference/regions.md)。

## <a name="common-headers"></a>常見標頭

### <a name="common-request-headers"></a>常見要求標頭

必須指定所有標頭:

- **授權**標頭的值必須為「承載器 =*TOKEN*}」,其中 [*TOKEN*] 是[服務存取權杖](../tokens.md)。

### <a name="common-response-headers"></a>常見回應標頭

所有回應都包含以下標頭:

- **MS-CV**標頭包含一個唯一字串,可用於跟蹤服務中的調用。

## <a name="endpoints"></a>端點

轉換服務提供三個 REST API 終結點,以:

- 使用與 Azure 遠端呈現帳戶關聯的存儲帳戶啟動模型轉換。 
- 使用提供的*共享存取簽名 (SAS)* 啟動模型轉換。
- 查詢轉換狀態

### <a name="start-conversion-using-a-linked-storage-account"></a>使用連結的儲存帳戶開始轉換
Azure 遠端呈現帳戶需要按照有關如何[連結存儲帳戶](../create-an-account.md#link-storage-accounts)的步驟訪問提供的存儲帳戶。

| 端點 | 方法 |
|-----------|:-----------|
| /v1/帳戶/**帳戶 ID**/轉換/建立 | POST |

返回在 JSON 文件中包裝的持續轉換的 ID。 欄位名稱為"轉換ID"。

#### <a name="request-body"></a>Request body


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
### <a name="start-conversion-using-provided-shared-access-signatures"></a>使用分享分享存取簽署開始轉換
如果您的 ARR 帳戶未連結到儲存帳戶,則此 REST 介面允許您使用*共享存取簽名 (SAS)* 提供訪問許可權。

| 端點 | 方法 |
|-----------|:-----------|
| /v1/帳戶/**帳戶 ID**/轉化/建立共享存取簽名 | POST |

返回在 JSON 文件中包裝的持續轉換的 ID。 欄位名稱為"轉換ID"。

#### <a name="request-body"></a>Request body

要求正文與上面建立 REST 呼叫相同,但輸入與輸出包含*分享存取簽章 (SAS) 權杖*。 這些權杖提供對儲存帳戶的訪問,用於讀取輸入和寫入轉換結果。

> [!NOTE]
> 這些 SAS URI 令牌是查詢字串,而不是完整的 URI。 


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
可以使用以下介面查詢從上述 REST 呼叫之一開始的正在進行的轉換的狀態:


| 端點 | 方法 |
|-----------|:-----------|
| /v1/帳戶/**帳戶 ID**/轉換/**轉換 Id** | GET |

傳回具有「狀態」欄位的 JSON 文件,該欄位可以具有以下值:

- "正在運行"
- "Success"
- "失敗"

如果狀態為"失敗",則將存在一個附加的"錯誤"欄位,其中將顯示一個包含錯誤資訊的"消息"子欄位。 其他日誌將上載到輸出容器。

## <a name="next-steps"></a>後續步驟

- [使用 Azure Blob 儲存體進行模型轉換](blob-storage.md)
- [模型轉換](model-conversion.md)
