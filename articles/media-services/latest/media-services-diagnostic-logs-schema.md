---
title: Azure 媒體服務診斷日誌架構 - Azure
description: 本文演示 Azure 媒體服務診斷日誌架構。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/20/2019
ms.author: juliako
ms.openlocfilehash: 37baed076ca074c1d558af36649e90959a0034c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75750885"
---
# <a name="diagnostic-logs-schemas"></a>診斷記錄結構描述

[Azure 監視器](../../azure-monitor/overview.md)使您能夠監視指標和診斷日誌，以説明您瞭解應用程式的性能。 您可以監視媒體服務診斷日誌，並為收集的指標和日誌創建警報和通知。 您可以將日誌發送到[Azure 存儲](https://azure.microsoft.com/services/storage/)，將它們資料流到[Azure 事件中心](https://azure.microsoft.com/services/event-hubs/)，並將它們匯出到[日誌分析](https://azure.microsoft.com/services/log-analytics/)，或使用協力廠商服務。

有關詳細資訊，請參閱[Azure 監視器指標](../../azure-monitor/platform/data-platform.md)和[Azure 監視器診斷日誌](../../azure-monitor/platform/platform-logs-overview.md)。

本文介紹了媒體服務診斷日誌架構。

## <a name="top-level-diagnostic-logs-schema"></a>最上層診斷記錄結構描述

有關頂級診斷日誌架構的詳細描述，請參閱[Azure 診斷日誌的支援服務、架構和類別](../../azure-monitor/platform/tutorial-dashboards.md)。

## <a name="key-delivery-log-schema"></a>金鑰交付日誌架構

### <a name="properties"></a>屬性

這些屬性特定于金鑰傳遞日誌架構。

|名稱|描述|
|---|---|
|keyId|請求的金鑰的 ID。|
|keyType|可以是以下值之一："清除"（無加密）、"公平播放"、"播放就緒"或"威文"。|
|policyName|策略的 Azure 資源管理器名稱。|
|tokenType|Token 類型。|
|狀態訊息|狀態訊息。|

### <a name="examples"></a>範例

金鑰傳遞請求架構的屬性。

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

[監視媒體服務指標和診斷日誌](media-services-metrics-diagnostic-logs.md)
