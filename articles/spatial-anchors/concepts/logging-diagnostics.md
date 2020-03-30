---
title: 記錄和診斷
description: 有關如何在 Azure 空間錨點中生成和檢索日誌記錄和診斷的深入說明。
author: ramonarguelles
manager: vriveras
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/22/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: f4359db1deda2295a66bcb97cf374d0fe9bc3ef7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74270134"
---
# <a name="logging-and-diagnostics-in-azure-spatial-anchors"></a>Azure 空間錨點中的日誌記錄和診斷

Azure 空間錨點提供了一個標準日誌記錄機制，該機制對應用開發很有用。 當您需要更多資訊進行調試時，空間錨點診斷日誌記錄模式非常有用。 診斷日誌記錄存儲環境映射。

## <a name="standard-logging"></a>標準日誌記錄
在空間錨點 API 中，您可以訂閱日誌記錄機制，以獲得有用的日誌來進行應用程式開發和調試。 標準日誌記錄 API 不會將環境圖片存儲在設備磁片上。 SDK 將這些日誌作為事件回檔提供。 您要將這些日誌集成到應用程式的日誌記錄機制中。

### <a name="configuration-of-log-messages"></a>日誌消息的配置
使用者有兩個感興趣的回檔。 下面的示例演示如何配置會話。

```csharp
    cloudSpatialAnchorSession = new CloudSpatialAnchorSession();
    . . .
    // set up the log level for the runtime session
    cloudSpatialAnchorSession.LogLevel = SessionLogLevel.Information;

    // configure the callback for the debug log
    cloudSpatialAnchorSession.OnLogDebug += CloudSpatialAnchorSession_OnLogDebug;

    // configure the callback for the error log
    cloudSpatialAnchorSession.Error += CloudSpatialAnchorSession_Error;
```

### <a name="events-and-properties"></a>事件和屬性

提供這些事件回檔來處理會話中的日誌和錯誤：

- [日誌級別](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.loglevel)：指定從運行時接收的事件的詳細級別。
- [OnLogDebug](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.onlogdebug)：提供標準的調試日誌事件。
- [錯誤](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.error)： 提供運行時認為是錯誤的日誌事件。

## <a name="diagnostics-logging"></a>診斷記錄

除了日誌記錄的標準操作模式外，空間錨點還具有診斷模式。 診斷模式捕獲環境映射並將其記錄到磁片。 您可以使用此模式調試某些類型的問題，例如無法預測地找到錨點。 啟用診斷日誌記錄以僅重現特定問題。 然後禁用它。 正常運行應用時，不要啟用診斷。

在與 Microsoft 進行支援交互時，Microsoft 代表可能會詢問您是否願意提交診斷包以供進一步調查。 在這種情況下，您可以決定啟用診斷並重現問題，以便提交診斷包。

如果您在未事先獲得 Microsoft 代表的確認的情況下向 Microsoft 提交診斷日誌，則提交將得不到答覆。

以下各節演示如何啟用診斷模式以及如何向 Microsoft 提交診斷日誌。

### <a name="enable-diagnostics-logging"></a>啟用診斷記錄

啟用診斷日誌記錄會話時，會話中的所有操作在本地檔案系統中都有相應的診斷日誌記錄。 在日誌記錄期間，環境映射將保存到磁片。

```csharp
private void ConfigureSession()
{
    cloudSpatialAnchorSession = new CloudSpatialAnchorSession();
    . . .

    // set up the log level for the runtime session
    cloudSpatialAnchorSession.LogLevel = SessionLogLevel.Information;

    // configure the callbacks for logging and errors
    cloudSpatialAnchorSession.OnLogDebug += CloudSpatialAnchorSession_OnLogDebug;
    cloudSpatialAnchorSession.Error += CloudSpatialAnchorSession_Error;

    // opt in to diagnostics logging of environment images
    // if this is enabled, the diagnostics bundle includes images of the environment captured by the session
    cloudSpatialAnchorSession.Diagnostics.ImagesEnabled = true;

    // set the level of detail to be collected in the diagnostics log by the session
    cloudSpatialAnchorSession.Diagnostics.LogLevel = SessionLogLevel.All;

    // set the max bundle size to capture the bug information
    cloudSpatialAnchorSession.Diagnostics.MaxDiskSizeInMB = 200;
    . . .
}
```

### <a name="submit-the-diagnostics-bundle"></a>提交診斷包

以下程式碼片段演示如何向 Microsoft 提交診斷包。 此捆綁包將包括啟用診斷後會話捕獲的環境映射。

```csharp
// method to handle the diagnostics bundle submission
private async Task CreateAndSubmitBundle()
{
    // create the diagnostics bundle manifest to collect the session information
    string path = await cloudSpatialAnchorSession
                              .Diagnostics
                              .CreateManifestAsync("Description of the issue");

    // submit the manifest and data to send feedback to Microsoft
    await cloudSpatialAnchorSession.Diagnostics.SubmitManifestAsync(path);
}
```

### <a name="parts-of-a-diagnostics-bundle"></a>診斷包的部件
診斷包可能包含以下資訊：

- **關鍵幀圖像**：啟用診斷時會話期間捕獲的環境圖像。
- **日誌**：記錄運行時記錄的事件。
- **會話中繼資料**：標識會話的中繼資料。
