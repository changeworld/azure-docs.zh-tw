---
title: Azure 媒體服務診斷記錄架構-Azure
description: 本文示範 Azure 媒體服務診斷記錄架構。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 8cfbe26458de630aaf411aade4a31cb4e9c72b17
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/02/2020
ms.locfileid: "89295422"
---
# <a name="diagnostic-logs-schemas"></a>診斷記錄結構描述

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

[Azure 監視器](../../azure-monitor/overview.md) 可讓您監視計量和診斷記錄，以協助您瞭解應用程式的執行情況。 您可以監視媒體服務診斷記錄，並為收集的計量和記錄建立警示和通知。 您可以將記錄傳送至 [Azure 儲存體](https://azure.microsoft.com/services/storage/)、將記錄串流至 [Azure 事件中樞](https://azure.microsoft.com/services/event-hubs/)，並將它們匯出至 [Log Analytics](https://azure.microsoft.com/services/log-analytics/)，或使用協力廠商服務。

如需詳細資訊，請參閱 [Azure 監視器計量](../../azure-monitor/platform/data-platform.md) 和 [Azure 監視器診斷記錄](../../azure-monitor/platform/platform-logs-overview.md)。

本文描述媒體服務診斷記錄架構。

## <a name="top-level-diagnostic-logs-schema"></a>最上層診斷記錄結構描述

如需最上層診斷記錄架構的詳細說明，請參閱 [Azure 診斷記錄支援的服務、架構和類別](../../azure-monitor/platform/resource-logs-schema.md)。

## <a name="key-delivery-log-schema"></a>金鑰傳遞記錄架構

### <a name="properties"></a>屬性

這些屬性是金鑰傳遞記錄架構特有的。

|Name|描述|
|---|---|
|keyId|要求的索引鍵識別碼。|
|keyType|可以是下列其中一個值：「清除」 (沒有加密) 、「FairPlay」、「PlayReady」或「Widevine」。|
|policyName|原則的 Azure Resource Manager 名稱。|
|tokenType|Token 類型。|
|>statusmessage|狀態訊息。|

### <a name="examples"></a>範例

金鑰傳遞要求架構的屬性。

```json
{
    "time": "2019-01-11T17:59:10.4908614Z",
    "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000/RESOURCEGROUPS/SBKEY/PROVIDERS/MICROSOFT.MEDIA/MEDIASERVICES/SBDNSTEST",
    "operationName": "MICROSOFT.MEDIA/MEDIASERVICES/CONTENTKEYS/READ",
    "operationVersion": "1.0",
    "category": "KeyDeliveryRequests",
    "resultType": "Succeeded",
    "resultSignature": "OK",
    "durationMs": 315,
    "identity": {
        "authorization": {
            "issuer": "http://testacs",
            "audience": "urn:test"
        },
        "claims": {
            "urn:microsoft:azure:mediaservices:contentkeyidentifier": "3321e646-78d0-4896-84ec-c7b98eddfca5",
            "iss": "http://testacs",
            "aud": "urn:test",
            "exp": "1547233138"
        }
    },
    "level": "Informational",
    "location": "uswestcentral",
    "properties": {
        "requestId": "b0243468-d8e5-4edf-a48b-d408e1661050",
        "keyType": "Clear",
        "keyId": "3321e646-78d0-4896-84ec-c7b98eddfca5",
        "policyName": "56a70229-82d0-4174-82bc-e9d3b14e5dbf",
        "tokenType": "JWT",
        "statusMessage": "OK"
    }
} 
```

```json
 {
    "time": "2019-01-11T17:59:33.4676382Z",
    "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000/RESOURCEGROUPS/SBKEY/PROVIDERS/MICROSOFT.MEDIA/MEDIASERVICES/SBDNSTEST",
    "operationName": "MICROSOFT.MEDIA/MEDIASERVICES/CONTENTKEYS/READ",
    "operationVersion": "1.0",
    "category": "KeyDeliveryRequests",
    "resultType": "Failed",
    "resultSignature": "Unauthorized",
    "durationMs": 2,
    "level": "Error",
    "location": "uswestcentral",
    "properties": {
        "requestId": "875af030-b77c-416b-b7e1-58f23ebec182",
        "keyType": "Clear",
        "keyId": "3321e646-78d0-4896-84ec-c7b98eddfca5",
        "policyName": "56a70229-82d0-4174-82bc-e9d3b14e5dbf",
        "tokenType": "None",
        "statusMessage": "No token present in authorization header or URL."
    }
} 
```

## <a name="additional-notes"></a>其他注意事項

* Widevine 是 Google Inc. 所提供的服務，並受到 Google Inc. 的服務條款和隱私權原則所約束。

## <a name="next-steps"></a>後續步驟

[監視媒體服務計量和診斷記錄](media-services-metrics-diagnostic-logs.md)
