---
title: 記錄和診斷
description: 如何在 Azure 空間錨點中產生和取出記錄和診斷的深入說明。
author: ramonarguelles
manager: vriveras
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/22/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: f4359db1deda2295a66bcb97cf374d0fe9bc3ef7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "74270134"
---
# <a name="logging-and-diagnostics-in-azure-spatial-anchors"></a>Azure 空間錨點中的記錄和診斷

Azure 空間錨點提供標準記錄機制，適用于應用程式開發。 當您需要更多的偵錯工具資訊時，空間錨點診斷記錄模式非常有用。 診斷記錄會儲存環境的影像。

## <a name="standard-logging"></a>標準記錄
在空間錨點 API 中，您可以訂閱記錄機制，以取得有用的記錄以進行應用程式開發和偵測。 標準記錄 Api 不會將環境的圖片儲存在裝置磁片上。 SDK 會提供這些記錄做為事件回呼。 您必須將這些記錄檔整合至應用程式的記錄機制。

### <a name="configuration-of-log-messages"></a>記錄訊息的設定
使用者有兩個有意義的回呼。 下列範例顯示如何設定會話。

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

系統會提供這些事件回呼，以處理來自會話的記錄和錯誤：

- [LogLevel](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.loglevel)：指定要從執行時間接收之事件的詳細資料層級。
- [OnLogDebug](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.onlogdebug)：提供標準的 debug 記錄事件。
- [錯誤](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.error)：提供執行時間視為錯誤的記錄事件。

## <a name="diagnostics-logging"></a>診斷記錄

除了用於記錄作業的標準模式以外，空間錨點也具有診斷模式。 診斷模式會捕獲環境的映射，並將其記錄到磁片中。 您可以使用此模式來偵測特定種類的問題，例如無法以可預測的方式找出錨點。 僅啟用診斷記錄以重現特定的問題。 然後將它停用。 當您正常執行應用程式時，請勿啟用診斷功能。

在與 Microsoft 的支援互動期間，Microsoft 代表可能會詢問您是否願意提交診斷配套以進行進一步調查。 在此情況下，您可能會決定啟用診斷並重現問題，以便您可以提交診斷配套。

如果您將診斷記錄提交給 Microsoft，但未事先收到 Microsoft 代表的通知，則提交將會未經解答。

下列各節說明如何啟用診斷模式，以及如何將診斷記錄提交給 Microsoft。

### <a name="enable-diagnostics-logging"></a>啟用診斷記錄

當您啟用診斷記錄的會話時，會話中的所有作業在本機檔案系統中都會有對應的診斷記錄。 在記錄期間，環境的映射會儲存至磁片。

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

### <a name="submit-the-diagnostics-bundle"></a>提交診斷配套

下列程式碼片段顯示如何將診斷配套提交給 Microsoft。 此配套會包含在啟用診斷之後，由會話所捕捉到的環境影像。

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

### <a name="parts-of-a-diagnostics-bundle"></a>診斷配套的元件
診斷配套可能包含下列資訊：

- 主要**畫面格影像**：啟用診斷時，在會話期間所捕捉到的環境影像。
- **記錄**：執行時間所記錄的記錄事件。
- **會話中繼資料**：可識別會話的中繼資料。
