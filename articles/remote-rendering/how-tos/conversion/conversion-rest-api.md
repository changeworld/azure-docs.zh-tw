---
title: 資產轉換 REST API
description: 說明如何透過 REST API 轉換資產
author: florianborn71
ms.author: flborn
ms.date: 02/04/2020
ms.topic: how-to
ms.openlocfilehash: 889a70005f1cbabaad525147b4661ea04886138a
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94445603"
---
# <a name="use-the-model-conversion-rest-api"></a>使用模型轉換 REST API

[模型轉換](model-conversion.md)服務是透過[REST API](https://en.wikipedia.org/wiki/Representational_state_transfer)來控制。 本文描述轉換服務 API 詳細資料。

## <a name="regions"></a>區域

查看要將要求傳送至的基底 Url 的 [可用區域清單](../../reference/regions.md) 。

## <a name="common-headers"></a>通用標頭

### <a name="common-request-headers"></a>常見的要求標頭

您必須為所有要求指定這些標頭：

- **授權** 標頭的值必須是「持有人 [ *權杖* ]」，其中 [ *token* ] 是 [服務存取權杖](../tokens.md)。

### <a name="common-response-headers"></a>常見的回應標頭

所有回應都包含下列標頭：

- **MS CV** 標頭包含唯一的字串，可用來追蹤服務內的呼叫。

## <a name="endpoints"></a>端點

轉換服務提供三個 REST API 端點：

- 使用與您的 Azure 遠端轉譯帳戶連結的儲存體帳戶來開始模型轉換。 
- 使用提供的 *共用存取簽章 (SAS)* 啟動模型轉換。
- 查詢轉換狀態

### <a name="start-conversion-using-a-linked-storage-account"></a>使用連結的儲存體帳戶開始轉換
您的 Azure 遠端轉譯帳戶必須遵循有關如何 [連結儲存體帳戶](../create-an-account.md#link-storage-accounts)的步驟，才能存取所提供的儲存體帳戶。

| 端點 | 方法 |
|-----------|:-----------|
| /v1/accounts/ **accountID** /conversions/create | POST |

傳回進行中轉換的識別碼，包裝在 JSON 檔中。 功能變數名稱為 "conversionId"。

#### <a name="request-body"></a>Request body

> [!NOTE]
> 下的所有專案 `input.folderPath` 都會被抓取，以在 Azure 上執行轉換。 如果 `input.folderPath` 未指定，將會取得容器的整個內容。 取出的所有 blob 和資料夾都必須有 [有效的 Windows 檔案名](/windows/win32/fileio/naming-a-file#naming-conventions)。

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
如果您的 ARR 帳戶未連結至您的儲存體帳戶，這個 REST 介面可讓您使用 *(SAS) 的共用存取* 簽章來提供存取權。

| 端點 | 方法 |
|-----------|:-----------|
| /v1/accounts/ **accountID** /conversions/createWithSharedAccessSignature | POST |

傳回進行中轉換的識別碼，包裝在 JSON 檔中。 功能變數名稱為 `conversionId` 。

#### <a name="request-body"></a>Request body

要求主體與上述的 create REST 呼叫相同，但輸入和輸出包含 *(SAS) 權杖的共用存取* 簽章。 這些權杖可讓您存取儲存體帳戶，以讀取輸入及寫入轉換結果。

> [!NOTE]
> 這些 SAS URI 權杖是查詢字串，而不是完整的 URI。 

> [!NOTE]
> 下的所有專案 `input.folderPath` 都會被抓取，以在 Azure 上執行轉換。 如果 `input.folderPath` 未指定，將會取得容器的整個內容。 取出的所有 blob 和資料夾都必須有 [有效的 Windows 檔案名](/windows/win32/fileio/naming-a-file#naming-conventions)。

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
使用上述其中一個 REST 呼叫開始進行中轉換的狀態，可使用下列介面進行查詢：


| 端點 | 方法 |
|-----------|:-----------|
| /v1/accounts/ **accountID** /conversions/ **conversionId** | GET |

傳回具有 "status" 欄位的 JSON 檔，其值可以是下列值：

- 建立
- 耗盡
- "Success"
- 發生

如果狀態為「失敗」，則會有一個額外的「錯誤」欄位，包含包含錯誤資訊的「訊息」子欄位。 系統會將其他記錄上傳到您的輸出容器。

## <a name="list-conversions"></a>清單轉換

若要取得帳戶的所有轉換清單，請使用介面：

| 端點 | 方法 |
|-----------|:-----------|
| /v1/accounts/ **accountID** /conversions？ Skiptoken = **skiptoken** | GET |

| 參數 | 必要 |
|-----------|:-----------|
| accountID | 是 |
| skiptoken | 否 |

傳回 json 檔，其中包含轉換的陣列及其詳細資料。 此查詢一次最多會傳回50個轉換。 如果要取得更多轉換，回應將會包含 **nextLink** 屬性，其中包含可查詢以取得下一組結果的 skipToken。

## <a name="next-steps"></a>後續步驟

- [使用 Azure Blob 儲存體進行模型轉換](blob-storage.md)
- [模型轉換](model-conversion.md)