---
title: 模組對應項 JSON 架構-Azure
description: 本主題說明 IoT Edge 上即時影片分析的模組對應項 JSON 架構。
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 8bd86bdc2c8de9ee586e785db2074fa772100420
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87053058"
---
# <a name="module-twin-json-schema"></a>模組對應項 JSON 架構

裝置 twins 是儲存裝置狀態資訊的 JSON 檔，包括中繼資料、設定和條件。 Azure IoT 中樞會維持連線到 IoT 中樞的每部裝置其裝置對應項。 如需詳細說明，請參閱 [瞭解及使用 IoT 中樞的模組 twins](../../iot-hub/iot-hub-devguide-module-twins.md)

本主題說明 IoT Edge 上即時影片分析的模組對應項 JSON 架構。

> [!NOTE]
> 在獲得存取媒體服務資源和媒體服務 API 的授權之前，您必須先進行驗證。 如需詳細資訊，請參閱 [存取 AZURE 媒體服務 API](../latest/media-services-apis-overview.md#accessing-the-azure-media-services-api)。

## <a name="module-twin-properties"></a>模組對應項屬性

IoT Edge 上的即時影片分析會公開下列模組對應項屬性。 

|屬性 |必要 |動態 |說明 |
|---|---|---|---|
|applicationDataDirectory |是 |否 |保存設定的已載入磁片區路徑。 |
|azureMediaServicesArmId |是 |否 |媒體服務帳戶的唯一 Azure 資源管理識別碼。|
|aadTenantId |是 |否 |客戶 Azure AD 租使用者識別碼。|
|aadServicePrincipalAppId |是 |是 |客戶 Azure AD AppId 建立。|
|aadServicePrincipalCertificate |是的<sup>*</sup>  |是 |客戶 Azure AD AppId 憑證建立。|
|aadServicePrincipalPassword |是的<sup>*</sup>  |是 |客戶 Azure AD AppId 密碼建立。|
|aadEndpoint |否 |否 |雲端特定的 Azure AD 端點。 <br/>預設： `https://login.microsoftonline.com` |
|aadResourceId |否 |否 |雲端特定的 Azure AD 物件/資源識別碼 <br/>預設： `https://management.core.windows.net/` |
|armEndpoint |否 |否 |雲端專用的 Azure 資源管理端點。 <br/>預設： `https://management.azure.com/` |
|diagnosticsLevel |否 |是 |事件詳細資訊： <br/>資訊 & # x02758;警告 & # x02758;錯誤 & # x02758;重大 & # x02758;沒有 |
|diagnosticsEventsOutputName |否 |是 |診斷事件的中樞輸出。 <br/> (空白表示診斷未發佈) |
|operationalEventsOutputName|否|是|操作事件的中樞輸出。<br/> (空白表示不會發行操作事件) 
|logLevel|否|是|下列其中之一： <br/>& # x000B7;詳細<br/>& # x000B7; (預設) 資訊<br/>& # x000B7;警告<br/>& # x000B7;錯誤<br/>& # x000B7;沒有|
|logCategories|否|是|下列以逗號分隔的清單： Application、MediaPipeline、Events <br/>預設值：應用程式、事件|
|debugLogsDirectory|否|是|Debug 記錄檔的目錄。 如果有產生記錄檔，則會停用 debug 記錄檔（如果沒有的話）。

<sup>*</sup>您必須提供服務主體憑證或密碼。 

您可以在不重新開機模組的情況下更新動態屬性。 您可以遵循「 [取得媒體服務 API](../latest/access-api-howto.md) 」一文中的指示，取得其中數個屬性的值。 

如需有關選擇性診斷設定角色的詳細資訊，請參閱 [監視和記錄](monitoring-logging.md) 的相關文章。

```
{ 
    "properties.desired": { 
        // Required 
        "applicationDataDirectory": "/var/lib/azuremediaservices", 
        "azureMediaServicesArmId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/<rgname>/providers/microsoft.media/mediaservices/<ams_account>", 
        "aadTenantId": "00000000-0000-0000-0000-000000000000", 
        "aadServicePrincipalAppId": "00000000-0000-0000-0000-000000000000", 
        "aadServicePrincipalPassword": "{Service principal password}", 

        // Optional API Access 
        "aadEndpoint": "https://<aad-endpoint>", 
        "aadResourceId": "https://management.core.windows.net/", 
        "armEndpoint": "https://management.azure.com/", 
        
        // Optional Diagnostics 
        "diagnosticsEventsOutputName": "lvaEdgeDiagnostics",
        "operationalEventsOutputName": "lvaEdgeOperational",
        "logLevel": "Information",
        "logCategories": "Application,Events"
    } 
} 
```

## <a name="next-steps"></a>接下來的步驟

[直接方法](direct-methods.md)
