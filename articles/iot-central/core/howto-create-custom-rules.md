---
title: 使用自訂規則和通知擴展 Azure IoT 中心 |微軟文檔
description: 作為解決方案開發人員，配置 IoT 中央應用程式，以便設備停止發送遙測時發送電子郵件通知。 此解決方案使用 Azure 流分析、Azure 函數和發送網格。
author: dominicbetts
ms.author: dobett
ms.date: 12/02/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 0e161cf83662df671b8cfb100ddc12c3b3e7359f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158141"
---
# <a name="extend-azure-iot-central-with-custom-rules-using-stream-analytics-azure-functions-and-sendgrid"></a>使用流分析、Azure 函數和發送網格使用自訂規則擴展 Azure IoT 中心



本說明指南介紹您作為解決方案開發人員如何使用自訂規則和通知擴展 IoT Central 應用程式。 該示例顯示當設備停止發送遙測資料時向操作員發送通知。 該解決方案使用[Azure 流分析](https://docs.microsoft.com/azure/stream-analytics/)查詢來檢測設備何時停止發送遙測資料。 流分析作業使用[Azure 函數](https://docs.microsoft.com/azure/azure-functions/)使用[SendGrid](https://sendgrid.com/docs/for-developers/partners/microsoft-azure/)發送通知電子郵件。

此操作指南演示如何將 IoT Central 擴展到其已經可以使用內置規則和操作執行的操作。

在此指南中，您將瞭解如何：

* 使用*連續資料匯出*從 IoT 中央應用程式資料流遙測。
* 創建流分析查詢，用於檢測設備何時停止發送資料。
* 使用 Azure 函數和發送網格服務發送電子郵件通知。

## <a name="prerequisites"></a>Prerequisites

若要完成此操作指南中的步驟，您必須具備有效的 Azure 訂用帳戶。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

### <a name="iot-central-application"></a>IoT Central 應用程式

在[Azure IoT 中央應用程式管理器](https://aka.ms/iotcentral)網站上創建具有以下設置的 IoT 中心應用程式：

| 設定 | 值 |
| ------- | ----- |
| 定價方案 | 標準 |
| 應用程式範本 | 店內分析 + 狀態監控 |
| 應用程式名稱 | 接受預設值或選擇您自己的名稱 |
| URL | 接受預設值或選擇您自己的唯一 URL 首碼 |
| 目錄 | Azure 活動目錄租戶 |
| Azure 訂用帳戶 | 您的 Azure 訂用帳戶 |
| 區域 | 您最近的區域 |

本文中的示例和螢幕截圖使用**美國**區域。 選擇靠近您的位置，並確保在同一區域中創建所有資源。

此應用程式範本包括兩個發送遙測的類比恒溫器設備。

### <a name="resource-group"></a>資源群組

使用[Azure 門戶創建](https://portal.azure.com/#create/Microsoft.ResourceGroup)名為 **"檢測停止設備"** 的資源組，以包含您創建的其他資源。 在 IoT 中央應用程式相同的位置創建 Azure 資源。

### <a name="event-hubs-namespace"></a>事件中樞命名空間

使用[Azure 門戶創建具有以下設置的事件中心命名空間](https://portal.azure.com/#create/Microsoft.EventHub)：

| 設定 | 值 |
| ------- | ----- |
| 名稱    | 選擇命名空間名稱 |
| 定價層 | 基本 |
| 訂用帳戶 | 您的訂用帳戶 |
| 資源群組 | 檢測已停止的設備 |
| Location | 美國東部 |
| 輸送量單位 | 1 |

### <a name="stream-analytics-job"></a>串流分析作業

使用[Azure 門戶創建具有以下設置的流分析作業](https://portal.azure.com/#create/Microsoft.StreamAnalyticsJob)：

| 設定 | 值 |
| ------- | ----- |
| 名稱    | 選擇您的工作名稱 |
| 訂用帳戶 | 您的訂用帳戶 |
| 資源群組 | 檢測已停止的設備 |
| Location | 美國東部 |
| 裝載環境 | Cloud |
| 串流單位 | 3 |

### <a name="function-app"></a>函式應用程式

使用[Azure 門戶創建](https://portal.azure.com/#create/Microsoft.FunctionApp)具有以下設置的函數應用：

| 設定 | 值 |
| ------- | ----- |
| 應用程式名稱    | 選擇函數應用名稱 |
| 訂用帳戶 | 您的訂用帳戶 |
| 資源群組 | 檢測已停止的設備 |
| OS | Windows |
| 主控方案 | 取用方案 |
| Location | 美國東部 |
| 執行階段堆疊 | .NET |
| 存放裝置 | 新建 |

### <a name="sendgrid-account"></a>發送網格帳戶

使用[Azure 門戶創建](https://portal.azure.com/#create/Sendgrid.sendgrid)具有以下設置的 SendGrid 帳戶：

| 設定 | 值 |
| ------- | ----- |
| 名稱    | 選擇您的 SendGrid 帳戶名稱 |
| 密碼 | 創建密碼 |
| 訂用帳戶 | 您的訂用帳戶 |
| 資源群組 | 檢測已停止的設備 |
| 定價層 | F1 免費 |
| 連絡人資訊 | 填寫所需資訊 |

創建所有必需資源後，**檢測停止設備**資源組如下所示：

![檢測已停止的設備資源組](media/howto-create-custom-rules/resource-group.png)

## <a name="create-an-event-hub"></a>建立事件中樞

您可以將 IoT 中央應用程式佈建為連續將遙測資料匯出到事件中心。 在本節中，您將創建一個事件中心來接收來自 IoT 中央應用程式的遙測資料。 事件中心將遙測資料傳遞到流分析作業進行處理。

1. 在 Azure 門戶中，導航到事件中心命名空間，然後選擇 **+ 事件中心**。
1. 命名事件中心**集中匯出**，然後選擇 **"創建**"。

事件中心命名空間類似于以下螢幕截圖：

![事件中樞命名空間](media/howto-create-custom-rules/event-hubs-namespace.png)

## <a name="get-sendgrid-api-key"></a>獲取發送網格 API 金鑰

您的功能應用需要一個 SendGrid API 金鑰來發送電子郵件。 要創建發送網格 API 金鑰，請進行：

1. 在 Azure 門戶中，導航到 SendGrid 帳戶。 然後選擇 **"管理**"以訪問您的 SendGrid 帳戶。
1. 在"發送網格"帳戶中，選擇 **"設置"，** 然後選擇**API 金鑰**。 選擇**創建 API 金鑰**：

    ![創建發送網格 API 金鑰](media/howto-create-custom-rules/sendgrid-api-keys.png)

1. 在 **"創建 API 金鑰"** 頁上，創建具有**完全存取權限**的名為**Azure 功能訪問**的金鑰。
1. 記下 API 金鑰，在配置函數應用時需要它。

## <a name="define-the-function"></a>定義函數

當流分析作業檢測到已停止的設備時，此解決方案使用 Azure 函數應用發送電子郵件通知。 要創建函數應用，

1. 在 Azure 門戶中，導航到**檢測停止設備**資源組中**的應用服務**實例。
1. 選擇**+** 以創建新函數。
1. 在 **"選擇開發環境**"頁上，選擇**門戶內**，然後選擇"**繼續**"。
1. 在 **"創建功能"** 頁上，選擇**Webhook + API，** 然後選擇"**創建**"。

門戶創建一個預設函數，稱為**HttpTrigger1**：

![預設 HTTP 觸發器功能](media/howto-create-custom-rules/default-function.png)

### <a name="configure-function-bindings"></a>配置函數綁定

要使用 SendGrid 發送電子郵件，您需要按如下方式配置函數的綁定：

1. 選擇 **"集成**"，選擇輸出**HTTP （$return），** 然後選擇 **"刪除**"。
1. 選擇 **= 新輸出**，然後選擇 **"發送網格**"，然後選擇 **"選擇**"。 選擇 **"安裝**"以安裝"發送網格"擴展。
1. 安裝完成後，選擇 **"使用函數傳回值**"。 添加有效的**To 位址**以接收電子郵件通知。  添加有效的**寄件者位址**以用作電子郵件寄件者。
1. 在 **"發送網格 API 金鑰應用設置"** 旁邊選擇**新**。 輸入**SendGridAPIKey**作為金鑰，以及您以前作為值記下的 SendGrid API 金鑰。 然後選擇 **"創建**"。
1. 選擇 **"保存**"以保存函數的 SendGrid 綁定。

集成設置類似于以下螢幕截圖：

![功能應用集成](media/howto-create-custom-rules/function-integrate.png)

### <a name="add-the-function-code"></a>添加函數代碼

要實現函數，請添加 C# 代碼以分析傳入的 HTTP 要求，併發送電子郵件，如下所示：

1. 在函數應用中選擇**HttpTrigger1**函數，並將 C# 代碼替換為以下代碼：

    ```csharp
    #r "Newtonsoft.Json"
    #r "..\bin\SendGrid.dll"

    using System;
    using SendGrid.Helpers.Mail;
    using Microsoft.Azure.WebJobs.Host;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;

    public static SendGridMessage Run(HttpRequest req, ILogger log)
    {
        string requestBody = new StreamReader(req.Body).ReadToEnd();
        log.LogInformation(requestBody);
        var notifications = JsonConvert.DeserializeObject<IList<Notification>>(requestBody);

        SendGridMessage message = new SendGridMessage();
        message.Subject = "Contoso device notification";

        var content = "The following device(s) have stopped sending telemetry:<br/><br/><table><tr><th>Device ID</th><th>Time</th></tr>";
        foreach(var notification in notifications) {
            log.LogInformation($"No message received - Device: {notification.deviceid}, Time: {notification.time}");
            content += $"<tr><td>{notification.deviceid}</td><td>{notification.time}</td></tr>";
        }
        content += "</table>";
        message.AddContent("text/html", content);

        return message;
    }

    public class Notification
    {
        public string deviceid { get; set; }
        public string time { get; set; }
    }
    ```

    在保存新代碼之前，您可能會看到一條錯誤訊息。

1. 選擇 **"保存**"以保存該功能。

### <a name="test-the-function-works"></a>測試函數工作

要在門戶中測試函數，請首先選擇代碼編輯器底部的 **"日誌**"。 然後選擇代碼編輯器右側的 **"測試**"。 使用以下 JSON 作為**請求正文**：

```json
[{"deviceid":"test-device-1","time":"2019-05-02T14:23:39.527Z"},{"deviceid":"test-device-2","time":"2019-05-02T14:23:50.717Z"},{"deviceid":"test-device-3","time":"2019-05-02T14:24:28.919Z"}]
```

函數日誌消息顯示在 **"日誌"** 面板中：

![函數日誌輸出](media/howto-create-custom-rules/function-app-logs.png)

幾分鐘後，"**到"** 電子郵件地址會收到包含以下內容的電子郵件：

```txt
The following device(s) have stopped sending telemetry:

Device ID    Time
test-device-1    2019-05-02T14:23:39.527Z
test-device-2    2019-05-02T14:23:50.717Z
test-device-3    2019-05-02T14:24:28.919Z
```

## <a name="add-stream-analytics-query"></a>添加流分析查詢

此解決方案使用流分析查詢來檢測設備何時停止發送遙測超過 120 秒。 查詢使用事件中心的遙測作為其輸入。 作業將查詢結果發送到函數應用。 在本節中，您可以配置流分析作業：

1. 在 Azure 門戶中，導航到流分析作業，在**作業拓撲**下選擇 **"輸入**"，選擇 **" 添加流輸入**"，然後選擇**事件中心**。
1. 使用下表中的資訊使用以前創建的事件中心配置輸入，然後選擇 **"保存**" ：

    | 設定 | 值 |
    | ------- | ----- |
    | 輸入別名 | 中央遙測 |
    | 訂用帳戶 | 您的訂用帳戶 |
    | 事件中樞命名空間 | 事件中心命名空間 |
    | 事件中樞名稱 | 使用現有 -**集中匯出** |

1. 在**作業拓撲**下，選擇 **"輸出**"，選擇 **+添加**，然後選擇**Azure 函數**。
1. 使用下表中的資訊配置輸出，然後選擇 **"保存**" ：

    | 設定 | 值 |
    | ------- | ----- |
    | 輸出別名 | 電子郵件通知 |
    | 訂用帳戶 | 您的訂用帳戶 |
    | 函式應用程式 | 您的功能應用 |
    | 函式  | HttpTrigger1 |

1. 在**作業拓撲**下，選擇 **"查詢"** 並將常設查詢替換為以下 SQL：

    ```sql
    with
    LeftSide as
    (
        SELECT
        -- Get the device ID from the message metadata and create a column
        GetMetadataPropertyValue([centraltelemetry], '[EventHub].[IoTConnectionDeviceId]') as deviceid1, 
        EventEnqueuedUtcTime AS time1
        FROM
        -- Use the event enqueued time for time-based operations
        [centraltelemetry] TIMESTAMP BY EventEnqueuedUtcTime
    ),
    RightSide as
    (
        SELECT
        -- Get the device ID from the message metadata and create a column
        GetMetadataPropertyValue([centraltelemetry], '[EventHub].[IoTConnectionDeviceId]') as deviceid2, 
        EventEnqueuedUtcTime AS time2
        FROM
        -- Use the event enqueued time for time-based operations
        [centraltelemetry] TIMESTAMP BY EventEnqueuedUtcTime
    )

    SELECT
        LeftSide.deviceid1 as deviceid,
        LeftSide.time1 as time
    INTO
        [emailnotification]
    FROM
        LeftSide
        LEFT OUTER JOIN
        RightSide 
        ON
        LeftSide.deviceid1=RightSide.deviceid2 AND DATEDIFF(second,LeftSide,RightSide) BETWEEN 1 AND 120
        where
        -- Find records where a device didn't send a message 120 seconds
        RightSide.deviceid2 is NULL
    ```

1. 選取 [儲存]****。
1. 要啟動流分析作業，請選擇 **"概述**"，然後 **"開始****"，然後"立即****"，然後開始**：

    ![串流分析](media/howto-create-custom-rules/stream-analytics.png)

## <a name="configure-export-in-iot-central"></a>在 IoT 中心設定匯出

在[Azure IoT 中央應用程式管理器](https://aka.ms/iotcentral)網站上，導航到從 Contoso 範本創建的 IoT 中央應用程式。 在本節中，您將應用程式佈建為將遙測資料從其類比設備資料流到事件中心。 要設定匯出：

1. 導航到 **"資料匯出"** 頁，選擇 **" 新建**"，然後選擇**Azure 事件中心**。
1. 使用以下設置設定匯出，然後選擇 **"保存**" ：

    | 設定 | 值 |
    | ------- | ----- |
    | 顯示名稱 | 匯出到事件中心 |
    | 啟用 | 另一 |
    | 事件中樞命名空間 | 事件中心命名空間名稱 |
    | 事件中樞 | 中央出口 |
    | 量測 | 另一 |
    | 裝置 | 關閉 |
    | 裝置範本 | 關閉 |

![連續資料匯出設定](media/howto-create-custom-rules/cde-configuration.png)

等待匯出狀態**為"正在運行"，** 然後再繼續。

## <a name="test"></a>測試

要測試解決方案，可以禁用從 IoT Central 到類比已停止設備的連續資料匯出：

1. 在 IoT 中心應用程式中，導航到**資料匯出**頁面並選擇 **"匯出到事件中心**"匯出配置。
1. "**已啟用"** 設置為 **"關閉**"，然後選擇 **"保存**"。
1. 至少兩分鐘後，"**到"** 電子郵件地址會收到一封或多封類似于以下示例內容的電子郵件：

    ```txt
    The following device(s) have stopped sending telemetry:

    Device ID         Time
    Thermostat-Zone1  2019-11-01T12:45:14.686Z
    ```

## <a name="tidy-up"></a>整理一下

要在此之後進行清理並避免不必要的成本，請刪除 Azure 門戶中的**檢測停止設備**資源組。

可以從**應用程式中的管理頁面**中刪除 IoT 中心應用程式。

## <a name="next-steps"></a>後續步驟

在此操作指南中，您已了解如何：

* 使用*連續資料匯出*從 IoT 中央應用程式資料流遙測。
* 創建流分析查詢，用於檢測設備何時停止發送資料。
* 使用 Azure 函數和發送網格服務發送電子郵件通知。

現在您已經瞭解如何創建自訂規則和通知，建議的下一步是瞭解如何[使用自訂分析擴展 Azure IoT 中心](howto-create-custom-analytics.md)。
