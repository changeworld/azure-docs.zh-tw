---
title: 通過 Azure 監視器監視媒體服務診斷日誌 |微軟文檔
description: 本文演示如何通過 Azure 監視器路由和查看診斷日誌。
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
ms.date: 07/08/2019
ms.author: juliako
ms.openlocfilehash: 4d4587c701a054828fc34785e2ae680fef47625d
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/29/2020
ms.locfileid: "80382914"
---
# <a name="monitor-media-services-diagnostic-logs"></a>監控媒體服務診斷日誌

[Azure 監視器](../../azure-monitor/overview.md)使您能夠監視指標和診斷日誌，以説明您瞭解應用程式的性能。 有關此功能的詳細說明，並瞭解為什麼要使用 Azure 媒體服務指標和診斷日誌，請參閱[監視媒體服務指標和診斷日誌](media-services-metrics-diagnostic-logs.md)。

本文介紹如何將資料路由到存儲帳戶，然後查看資料。

## <a name="prerequisites"></a>Prerequisites

- [創建媒體服務帳戶](create-account-cli-how-to.md)。
- 查看[監視器媒體服務指標和診斷日誌](media-services-metrics-diagnostic-logs.md)。

## <a name="route-data-to-the-storage-account-using-the-portal"></a>使用門戶將資料路由到存儲帳戶

1. 在 https://portal.azure.com 上登入 Azure 入口網站。
1. 導航到媒體服務帳戶，然後按一下 **"監視器**下的**診斷設置**"。 在這裡，您會看到訂用帳戶中所有透過 Azure 監視器而產生監視資料的資源清單。

    ![診斷設定區段](media/media-services-diagnostic-logs/logs01.png)

1. 按一下「新增診斷設定」****。

   資源診斷設定可定義應該從特定資源路由傳送「什麼」** 監視資料，以及監視資料應該傳送至「何處」**。

1. 在出現的區段中，為您的設定指定**名稱**，並勾選 [封存至儲存體帳戶]**** 方塊。

    選擇要向其發送日誌的存儲帳戶，然後按 **"確定**"。
1. 勾選 [記錄]**** 和 [計量]**** 下的所有方塊。 根據資源類型而定，您可能只有其中一個選項可用。 這些核取方塊可控制要將該資源類型的哪幾種記錄和計量資料傳送至您選取的目的地，在此案例中是儲存體帳戶。

   ![診斷設定區段](media/media-services-diagnostic-logs/logs02.png)
1. 將 [保留期 (天數)]**** 滑桿設為 30。 此滑桿可設定監視資料在儲存體帳戶中的保留天數。 Azure 監視器會自動刪除比指定天數更舊的資料。 保留天數為 0 會無限期地儲存資料。
1. 按一下 [儲存]****。

來自資源的監視資料現在開始流入儲存體帳戶。

## <a name="route-data-to-the-storage-account-using-the-azure-cli"></a>使用 Azure CLI 將資料路由到存儲帳戶

要在存儲帳戶中啟用診斷日誌的存儲，將運行以下`az monitor diagnostic-settings`Azure CLI 命令：

```azurecli-interactive
az monitor diagnostic-settings create --name <diagnostic name> \
    --storage-account <name or ID of storage account> \
    --resource <target resource object ID> \
    --resource-group <storage account resource group> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true,
        "retentionPolicy": {
            "days": <# days to retain>,
            "enabled": true
        }
    }]'
```

例如：

```azurecli-interactive
az monitor diagnostic-settings create --name amsv3diagnostic \
    --storage-account storageaccountforams  \
    --resource "/subscriptions/00000000-0000-0000-0000-0000000000/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount" \
    --resource-group "amsResourceGroup" \
    --logs '[{"category": "KeyDeliveryRequests",  "enabled": true, "retentionPolicy": {"days": 3, "enabled": true }}]'
```

## <a name="view-data-in-the-storage-account-using-the-portal"></a>使用門戶查看存儲帳戶中的資料

如果您遵循上述步驟操作，資料已經開始流向儲存體帳戶。

您可能需要等候長達五分鐘，事件才會出現在儲存體帳戶中。

1. 在入口網站中，在左側導覽列尋找並瀏覽至 [儲存體帳戶]**** 區段。
1. 識別您在上一節所建立的儲存體帳戶並按一下。
1. 按一下**Blob，** 然後在標有**見解-日誌-鍵傳遞請求**的容器上按一下 。 這是其中有日誌的容器。 監視資料按資源識別碼（然後按日期和時間）分解為容器。
1. 依資源識別碼、日期和時間按一下容器，以瀏覽至 PT1H.json 檔案。 按一下 PT1H.json 檔案，然後按一下 [下載]****。

 您現在可以檢視已儲存在儲存體帳戶中的 JSON 事件。

### <a name="examples-of-pt1hjson"></a>PT1H.json 示例

#### <a name="clear-key-delivery-log"></a>清除金鑰傳遞日誌

```json
{
  "time": "2019-05-21T00:07:33.2820450Z",
  "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000000/RESOURCEGROUPS/amsResourceGroup/PROVIDERS/MICROSOFT.MEDIA/MEDIASERVICES/AMSACCOUNT",
  "operationName": "MICROSOFT.MEDIA/MEDIASERVICES/CONTENTKEYS/READ",
  "operationVersion": "1.0",
  "category": "KeyDeliveryRequests",
  "resultType": "Succeeded",
  "resultSignature": "OK",
  "durationMs": 253,
  "identity": {
    "authorization": {
      "issuer": "myIssuer",
      "audience": "myAudience"
    },
    "claims": {
      "urn:microsoft:azure:mediaservices:contentkeyidentifier": "e4276e1d-c012-40b1-80d0-ac15808b9277",
      "nbf": "1558396914",
      "exp": "1558400814",
      "iss": "myIssuer",
      "aud": "myAudience"
    }
  },
  "level": "Informational",
  "location": "westus2",
  "properties": {
    "requestId": "fb5c2b3a-bffa-4434-9c6f-73d689649add",
    "keyType": "Clear",
    "keyId": "e4276e1d-c012-40b1-80d0-ac15808b9277",
    "policyName": "SharedContentKeyPolicyUsedByAllAssets",
    "tokenType": "JWT",
    "statusMessage": "OK"
  }
}
```

#### <a name="widevine-encrypted-key-delivery-log"></a>寬文加密金鑰傳遞日誌

```json
{
  "time": "2019-05-20T23:15:22.7088747Z",
  "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000000/RESOURCEGROUPS/amsResourceGroup/PROVIDERS/MICROSOFT.MEDIA/MEDIASERVICES/AMSACCOUNT",
  "operationName": "MICROSOFT.MEDIA/MEDIASERVICES/CONTENTKEYS/READ",
  "operationVersion": "1.0",
  "category": "KeyDeliveryRequests",
  "resultType": "Succeeded",
  "resultSignature": "OK",
  "durationMs": 69,
  "identity": {
    "authorization": {
      "issuer": "myIssuer",
      "audience": "myAudience"
    },
    "claims": {
      "urn:microsoft:azure:mediaservices:contentkeyidentifier": "0092d23a-0a42-4c5f-838e-6d1bbc6346f8",
      "nbf": "1558392430",
      "exp": "1558396330",
      "iss": "myIssuer",
      "aud": "myAudience"
    }
  },
  "level": "Informational",
  "location": "westus2",
  "properties": {
    "requestId": "49613dd2-16aa-4595-a6e0-4e68beae6d37",
    "keyType": "Widevine",
    "keyId": "0092d23a-0a42-4c5f-838e-6d1bbc6346f8",
    "policyName": "DRMContentKeyPolicy",
    "tokenType": "JWT",
    "statusMessage": "OK"
  }
}
```

## <a name="additional-notes"></a>其他注意事項

* Widevine 是 Google Inc. 所提供的服務，並受到 Google Inc. 的服務條款和隱私權原則所約束。

## <a name="see-also"></a>另請參閱

* [Azure 監視器指標](../../azure-monitor/platform/data-platform.md)
* [Azure 監視器診斷日誌](../../azure-monitor/platform/platform-logs-overview.md)
* [如何從 Azure 資源收集和使用日誌資料](../../azure-monitor/platform/platform-logs-overview.md)

## <a name="next-steps"></a>後續步驟

[監視計量](media-services-metrics-howto.md)
