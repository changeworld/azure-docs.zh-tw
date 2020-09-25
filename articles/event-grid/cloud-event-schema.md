---
title: 使用 Azure 事件方格的 CloudEvents v1.0 架構
description: 說明如何針對 Azure 事件方格中的事件使用 CloudEvents v1.0 架構。 此服務支援雲端事件的 JSON 實作中的事件。
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: d17e92c28784ca31f3c9809c93e885b22c6a38d0
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91324173"
---
# <a name="cloudevents-v10-schema-with-azure-event-grid"></a>使用 Azure 事件方格的 CloudEvents v1.0 架構

除了[預設事件結構描述](event-schema.md)以外，Azure 事件方格在本質上也支援 [CloudEvents v1.0 的 JSON 實作](https://github.com/cloudevents/spec/blob/v1.0/json-format.md)和 [HTTP 通訊協定繫結](https://github.com/cloudevents/spec/blob/v1.0/http-protocol-binding.md)中的事件。 [CloudEvents](https://cloudevents.io/) 是用來說明事件資料的[開放式規格](https://github.com/cloudevents/spec/blob/v1.0/spec.md)。

CloudEvents 提供用以發佈和取用雲端型事件的常見事件結構描述，可簡化互通性。 此結構描述可支援統一的工具、路由和處理事件的標準方式，以及將外部事件結構描述還原序列化的通用方式。 透過通用結構描述，您將可更輕鬆地跨平台整合工作。

目前有數個[共同作業者](https://github.com/cloudevents/spec/blob/master/community/contributors.md) (包括 Microsoft) 正透過 [Cloud Native Computing Foundation](https://www.cncf.io/) 建置 CloudEvents。 目前可用的版本為 1.0。

本文說明如何使用事件方格來 CloudEvents 架構。

## <a name="sample-event-using-cloudevents-schema"></a>使用 CloudEvents 架構的範例事件

下列範例說明 CloudEvents 格式的 Azure Blob 儲存體事件：

``` JSON
{
    "specversion": "1.0",
    "type": "Microsoft.Storage.BlobCreated",  
    "source": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-account}",
    "id": "9aeb0fdf-c01e-0131-0922-9eb54906e209",
    "time": "2019-11-18T15:13:39.4589254Z",
    "subject": "blobServices/default/containers/{storage-container}/blobs/{new-file}",
    "dataschema": "#",
    "data": {
        "api": "PutBlockList",
        "clientRequestId": "4c5dd7fb-2c48-4a27-bb30-5361b5de920a",
        "requestId": "9aeb0fdf-c01e-0131-0922-9eb549000000",
        "eTag": "0x8D76C39E4407333",
        "contentType": "image/png",
        "contentLength": 30699,
        "blobType": "BlockBlob",
        "url": "https://gridtesting.blob.core.windows.net/testcontainer/{new-file}",
        "sequencer": "000000000000000000000000000099240000000000c41c18",
        "storageDiagnostics": {
            "batchId": "681fe319-3006-00a8-0022-9e7cde000000"
        }
    }
}
```

如需 CloudEvents v1.0 中的可用欄位、其類型和定義的詳細說明，請參閱[這裡](https://github.com/cloudevents/spec/blob/v1.0/spec.md#required-attributes)。

在 CloudEvents 結構描述中傳遞的事件標頭值與事件方格結構描述的該值相同，不同之處在於 `content-type`。 針對 CloudEvents 結構描述，該標頭值是 `"content-type":"application/cloudevents+json; charset=utf-8"`。 針對事件方格結構描述，該標頭值是 `"content-type":"application/json; charset=utf-8"`。

## <a name="event-grid-for-cloudevents"></a>CloudEvents 的事件方格

您可以使用事件方格來輸入和輸出 CloudEvents 結構描述中的事件。 您可以將 CloudEvents 用於系統事件 (例如 Blob 儲存體事件和 IoT 中樞事件) 及自訂事件。 它也可以在線上來回轉換這些事件。


| 輸入結構描述       | 輸出結構描述
|--------------------|---------------------
| CloudEvents 格式 | CloudEvents 格式
| 事件方格格式  | CloudEvents 格式
| 事件方格格式  | 事件方格格式

對於所有的事件結構描述，事件方格在發佈至事件方格主題和建立事件訂閱時，都需要進行驗證。 如需詳細資訊，請參閱 [Event Grid 安全性和驗證](security-authentication.md)。

## <a name="next-steps"></a>後續步驟
瞭解 [如何搭配事件方格使用 CloudEvents v1.0 架構](cloudevents-schema.md)。  
