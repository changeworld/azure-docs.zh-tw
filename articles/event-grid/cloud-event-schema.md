---
title: 將 Azure 事件方格用於 CloudEvents 結構描述中的事件
description: 說明如何在 Azure 事件方格中使用事件的 CloudEvents 架構。 服務支援在雲端事件的 JSON 實行中的事件。
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: babanisa
ms.openlocfilehash: 127095bef2c67a93097bf90bea54ca1b44b16c58
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81394380"
---
# <a name="cloudevents-v10-schema-with-event-grid"></a>具有事件方格的 CloudEvents v1.0 架構

除了其[預設事件架構](event-schema.md)之外，Azure 事件方格原本就支援 CloudEvents V1.0 和[HTTP 通訊協定](https://github.com/cloudevents/spec/blob/v1.0/http-protocol-binding.md)系結的[JSON 實](https://github.com/cloudevents/spec/blob/v1.0/json-format.md)值中的事件。 [CloudEvents](https://cloudevents.io/) 是用來說明事件資料的[開放式規格](https://github.com/cloudevents/spec/blob/v1.0/spec.md)。

CloudEvents 提供用以發佈和取用雲端型事件的常見事件結構描述，可簡化互通性。 此結構描述可支援統一的工具、路由和處理事件的標準方式，以及將外部事件結構描述還原序列化的通用方式。 透過通用結構描述，您將可更輕鬆地跨平台整合工作。

目前有數個[共同作業者](https://github.com/cloudevents/spec/blob/master/community/contributors.md) (包括 Microsoft) 正透過 [Cloud Native Computing Foundation](https://www.cncf.io/) 建置 CloudEvents。 它目前以1.0 版的形式提供。

本文說明具有事件方格的 CloudEvents 架構。

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

如需可用欄位、其類型和定義的詳細說明，請參閱[這裡](https://github.com/cloudevents/spec/blob/v1.0/spec.md#required-attributes)： CloudEvents v1.0。

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
請參閱[如何搭配使用 CloudEvents v1.0 架構與事件方格](cloudevents-schema.md)。  
