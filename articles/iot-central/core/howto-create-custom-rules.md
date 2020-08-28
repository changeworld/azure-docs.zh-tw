---
title: 使用自訂規則和通知擴充 Azure IoT Central |Microsoft Docs
description: 作為解決方案開發人員，設定 IoT Central 應用程式在裝置停止傳送遙測時傳送電子郵件通知。 此解決方案會使用 Azure 串流分析、Azure Functions 和 SendGrid。
author: dominicbetts
ms.author: dobett
ms.date: 12/02/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: mvc, devx-track-csharp
manager: philmea
ms.openlocfilehash: 572b5328a433839dafbfe23eb7207dfaeb9ea309
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2020
ms.locfileid: "89017850"
---
# <a name="extend-azure-iot-central-with-custom-rules-using-stream-analytics-azure-functions-and-sendgrid"></a>使用串流分析、Azure Functions 和 SendGrid 的自訂規則擴充 Azure IoT Central



本操作指南會示範如何以解決方案開發人員的身分，使用自訂規則和通知擴充您的 IoT Central 應用程式。 此範例顯示當裝置停止傳送遙測時，傳送通知給操作員。 解決方案會使用 [Azure 串流分析](https://docs.microsoft.com/azure/stream-analytics/) 查詢來偵測裝置何時停止傳送遙測。 串流分析作業會使用 [Azure Functions](https://docs.microsoft.com/azure/azure-functions/) ，透過 [SendGrid](https://sendgrid.com/docs/for-developers/partners/microsoft-azure/)傳送通知電子郵件。

本操作指南會示範如何將 IoT Central 延伸到超出內建規則和動作所能執行的作業。

在本操作指南中，您將瞭解如何：

* 使用 *連續資料匯出*從 IoT Central 應用程式串流遙測資料。
* 建立串流分析查詢，以偵測裝置停止傳送資料的時間。
* 使用 Azure Functions 和 SendGrid 服務傳送電子郵件通知。

## <a name="prerequisites"></a>先決條件

若要完成此操作指南中的步驟，您必須具備有效的 Azure 訂用帳戶。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

### <a name="iot-central-application"></a>IoT Central 應用程式

使用下列設定，在 [Azure IoT Central application manager](https://aka.ms/iotcentral) 網站上建立 IoT Central 應用程式：

| 設定 | 值 |
| ------- | ----- |
| 定價方案 | 標準 |
| 應用程式範本 | 店內分析-條件監視 |
| 應用程式名稱 | 接受預設值或選擇您自己的名稱 |
| URL | 接受預設值，或選擇您自己唯一的 URL 前置詞 |
| 目錄 | 您的 Azure Active Directory 租使用者 |
| Azure 訂用帳戶 | 您的 Azure 訂用帳戶 |
| 區域 | 您最近的區域 |

本文中的範例和螢幕擷取畫面會使用 **美國** 地區。 選擇接近您的位置，並確定您在相同區域中建立所有資源。

此應用程式範本包含兩個會傳送遙測資料的模擬控溫器裝置。

### <a name="resource-group"></a>資源群組

使用 Azure 入口網站建立名為**DetectStoppedDevices**的[資源群組](https://portal.azure.com/#create/Microsoft.ResourceGroup)，以包含您所建立的其他資源。 在與您的 IoT Central 應用程式相同的位置中建立您的 Azure 資源。

### <a name="event-hubs-namespace"></a>事件中樞命名空間

使用 Azure 入口網站來建立具有下列設定的 [事件中樞命名空間](https://portal.azure.com/#create/Microsoft.EventHub) ：

| 設定 | 值 |
| ------- | ----- |
| 名稱    | 選擇您的命名空間名稱 |
| 定價層 | 基本 |
| 訂用帳戶 | 您的訂用帳戶 |
| 資源群組 | DetectStoppedDevices |
| 位置 | 美國東部 |
| 輸送量單位 | 1 |

### <a name="stream-analytics-job"></a>串流分析作業

使用 Azure 入口網站來建立具有下列設定的 [串流分析作業](https://portal.azure.com/#create/Microsoft.StreamAnalyticsJob)  ：

| 設定 | 值 |
| ------- | ----- |
| 名稱    | 選擇您的作業名稱 |
| 訂用帳戶 | 您的訂用帳戶 |
| 資源群組 | DetectStoppedDevices |
| 位置 | 美國東部 |
| 裝載環境 | Cloud |
| 串流單位 | 3 |

### <a name="function-app"></a>函數應用程式

使用 [Azure 入口網站來建立](https://portal.azure.com/#create/Microsoft.FunctionApp) 具有下列設定的函式應用程式：

| 設定 | 值 |
| ------- | ----- |
| 應用程式名稱    | 選擇您的函數應用程式名稱 |
| 訂用帳戶 | 您的訂用帳戶 |
| 資源群組 | DetectStoppedDevices |
| OS | Windows |
| 主控方案 | 取用方案 |
| 位置 | 美國東部 |
| 執行階段堆疊 | .NET |
| 儲存體 | 新建 |

### <a name="sendgrid-account"></a>SendGrid 帳戶

使用 [Azure 入口網站來建立](https://portal.azure.com/#create/Sendgrid.sendgrid) 具有下列設定的 SendGrid 帳戶：

| 設定 | 值 |
| ------- | ----- |
| 名稱    | 選擇您的 SendGrid 帳戶名稱 |
| 密碼 | 建立密碼 |
| 訂用帳戶 | 您的訂用帳戶 |
| 資源群組 | DetectStoppedDevices |
| 定價層 | F1 免費 |
| 連絡人資訊 | 填寫必要資訊 |

當您已建立所有必要的資源之後，您的 **DetectStoppedDevices** 資源群組看起來會像下列螢幕擷取畫面：

![偵測已停止的裝置資源群組](media/howto-create-custom-rules/resource-group.png)

## <a name="create-an-event-hub"></a>建立事件中樞

您可以設定 IoT Central 的應用程式，以持續將遙測匯出至事件中樞。 在本節中，您會建立事件中樞，以從您的 IoT Central 應用程式接收遙測。 事件中樞會將遙測傳遞給您的串流分析作業，以進行處理。

1. 在 Azure 入口網站中，流覽至您的事件中樞命名空間，然後選取 [ **+ 事件中樞**]。
1. 為您的事件中樞命名 **centralexport**，然後選取 [ **建立**]。

您的事件中樞命名空間如下列螢幕擷取畫面所示：

![事件中樞命名空間](media/howto-create-custom-rules/event-hubs-namespace.png)

## <a name="get-sendgrid-api-key"></a>取得 SendGrid API 金鑰

您的函數應用程式需要 SendGrid API 金鑰，才能傳送電子郵件訊息。 若要建立 SendGrid API 金鑰：

1. 在 Azure 入口網站中，流覽至您的 SendGrid 帳戶。 然後選擇 [ **管理** ] 以存取您的 SendGrid 帳戶。
1. 在您的 SendGrid 帳戶中，選擇 [ **設定**]，然後選擇 [ **API 金鑰**]。 選擇 [ **建立 API 金鑰**]：

    ![建立 SendGrid API 金鑰](media/howto-create-custom-rules/sendgrid-api-keys.png)

1. 在 [**建立 API 金鑰**] 頁面上，使用 [**完整訪問**許可權] 建立名為**AzureFunctionAccess**的金鑰。
1. 請記下 API 金鑰，您在設定函數應用程式時需要用到它。

## <a name="define-the-function"></a>定義函數

當串流分析作業偵測到已停止的裝置時，此解決方案會使用 Azure Functions 應用程式傳送電子郵件通知。 若要建立您的函數應用程式：

1. 在 Azure 入口網站中，流覽至**DetectStoppedDevices**資源群組中的**App Service**實例。
1. 選取 **+** 即可建立新的函式。
1. 在 [ **選擇開發環境** ] 頁面上，選擇 [ **入口網站** ]，然後選取 [ **繼續**]。
1. 在 [ **建立函數** ] 頁面上，選擇 [ **Webhook + API** ]，然後選取 [ **建立**]。

入口網站會建立名為 **>HTTPtrigger1**的預設函數：

![預設 HTTP 觸發程式函式](media/howto-create-custom-rules/default-function.png)

### <a name="configure-function-bindings"></a>設定函數系結

若要使用 SendGrid 傳送電子郵件，您需要設定函數的系結，如下所示：

1. 選取 [ **整合**]，選擇輸出 **HTTP ($return) **，然後選取 [ **刪除**]。
1. 選擇 [ **+ 新增輸出**]，然後選擇 [ **SendGrid**]，再選擇 [ **選取**]。 選擇 [ **安裝** ] 以安裝 SendGrid 延伸模組。
1. 當安裝完成時，請選取 [ **使用函數傳回值**]。 新增有效的 **位址** 以接收電子郵件通知。  新增有效 **的發** 件人位址，以做為電子郵件寄件者使用。
1. 選取 [ **SENDGRID API 金鑰應用程式設定**] 旁的 [**新增**]。 輸入 **SendGridAPIKey** 做為金鑰，並輸入您先前記下的 SendGrid API 金鑰作為值。 然後選取 [建立]。
1. 選擇 [ **儲存** ] 以儲存您函式的 SendGrid 系結。

整合設定看起來會像下列螢幕擷取畫面：

![函數應用程式整合](media/howto-create-custom-rules/function-integrate.png)

### <a name="add-the-function-code"></a>新增函式程式碼

若要執行您的函式，請新增 c # 程式碼來剖析傳入的 HTTP 要求，並傳送電子郵件，如下所示：

1. 選擇您函式應用程式中的 **>HTTPtrigger1** 函式，並將 c # 程式碼取代為下列程式碼：

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

    在您儲存新的程式碼之前，您可能會看到錯誤訊息。

1. 選取 [ **儲存** ] 以儲存函數。

### <a name="test-the-function-works"></a>測試函數的運作方式

若要在入口網站中測試函式，請先選擇程式碼編輯器底部的 [ **記錄** 檔]。 然後選擇 [程式碼編輯器] 右邊的 [ **測試** ]。 使用下列 JSON 作為 **要求主體**：

```json
[{"deviceid":"test-device-1","time":"2019-05-02T14:23:39.527Z"},{"deviceid":"test-device-2","time":"2019-05-02T14:23:50.717Z"},{"deviceid":"test-device-3","time":"2019-05-02T14:24:28.919Z"}]
```

函數記錄檔訊息會出現在 [ **記錄** ] 面板中：

![函數記錄檔輸出](media/howto-create-custom-rules/function-app-logs.png)

幾分鐘後，電子郵件地址 **就** 會收到包含下列內容的電子郵件：

```txt
The following device(s) have stopped sending telemetry:

Device ID    Time
test-device-1    2019-05-02T14:23:39.527Z
test-device-2    2019-05-02T14:23:50.717Z
test-device-3    2019-05-02T14:24:28.919Z
```

## <a name="add-stream-analytics-query"></a>新增串流分析查詢

此解決方案使用串流分析查詢來偵測裝置停止傳送遙測超過120秒的時間。 查詢會使用來自事件中樞的遙測做為其輸入。 作業會將查詢結果傳送至函數應用程式。 在本節中，您會設定串流分析作業：

1. 在 Azure 入口網站中，流覽至您的串流分析作業，在 [ **作業拓撲** ] 下選取 [ **輸入**]，選擇 [ **+ 新增資料流程輸入**]，然後選擇 [ **事件中樞**]。
1. 使用下表中的資訊，使用您先前建立的事件中樞來設定輸入，然後選擇 [ **儲存**]：

    | 設定 | 值 |
    | ------- | ----- |
    | 輸入別名 | centraltelemetry |
    | 訂用帳戶 | 您的訂用帳戶 |
    | 事件中樞命名空間 | 您的事件中樞命名空間 |
    | 事件中樞名稱 | 使用現有的- **centralexport** |

1. 在 [ **作業拓撲**] 下，選取 [ **輸出**]，選擇 [ **+ 新增**]，然後選擇 [ **Azure 函數**]。
1. 使用下表中的資訊來設定輸出，然後選擇 [ **儲存**]：

    | 設定 | 值 |
    | ------- | ----- |
    | 輸出別名 | emailnotification |
    | 訂用帳戶 | 您的訂用帳戶 |
    | 函數應用程式 | 您的函數應用程式 |
    | 函式  | >HTTPtrigger1 |

1. 在 [ **作業拓撲**] 下，選取 [ **查詢** ]，並將現有的查詢取代為下列 SQL：

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

1. 選取 [儲存]  。
1. 若要啟動串流分析作業，請選擇 [總覽]，然後依序選擇 **[****開始**]、[開始 **]，然後****開始**：

    ![串流分析](media/howto-create-custom-rules/stream-analytics.png)

## <a name="configure-export-in-iot-central"></a>在 IoT Central 中設定匯出

在 [Azure IoT Central 應用程式管理員](https://aka.ms/iotcentral) 網站上，流覽至您從 Contoso 範本建立的 IoT Central 應用程式。 在本節中，您會設定應用程式，以將遙測從模擬裝置串流至您的事件中樞。 若要設定匯出：

1. 流覽至 [ **資料匯出** ] 頁面，選取 [ **+ 新增**]，然後 **Azure 事件中樞**]。
1. 使用下列設定來設定匯出，然後選取 [ **儲存**]：

    | 設定 | 值 |
    | ------- | ----- |
    | 顯示名稱 | 匯出至事件中樞 |
    | 已啟用 | 開啟 |
    | 事件中樞命名空間 | 您的事件中樞命名空間名稱 |
    | 事件中樞 | centralexport |
    | 量測 | 開啟 |
    | 裝置 | 關閉 |
    | 裝置範本 | 關閉 |

![連續資料匯出設定](media/howto-create-custom-rules/cde-configuration.png)

等候匯出狀態為 [ **正在** 執行]，然後再繼續。

## <a name="test"></a>測試

若要測試解決方案，您可以停用從 IoT Central 到模擬已停止裝置的連續資料匯出：

1. 在您的 IoT Central 應用程式中，流覽至 [ **資料匯出** ] 頁面，然後選取 [ **匯出至事件中樞** 匯出設定]。
1. 將 [ **啟用** ] 設定為 [ **關閉** ] 並選擇 [ **儲存**]。
1. 至少兩分鐘之後，收件 **者電子郵件** 位址就會收到一或多個看起來像下列範例內容的電子郵件：

    ```txt
    The following device(s) have stopped sending telemetry:

    Device ID         Time
    Thermostat-Zone1  2019-11-01T12:45:14.686Z
    ```

## <a name="tidy-up"></a>整理一下

若要在此操作說明之後整理，並避免不必要的成本，請刪除 Azure 入口網站中的 **DetectStoppedDevices** 資源群組。

您可以從應用程式內的 **管理** 頁面刪除 IoT Central 應用程式。

## <a name="next-steps"></a>後續步驟

在此操作指南中，您已了解如何：

* 使用 *連續資料匯出*從 IoT Central 應用程式串流遙測資料。
* 建立串流分析查詢，以偵測裝置停止傳送資料的時間。
* 使用 Azure Functions 和 SendGrid 服務傳送電子郵件通知。

現在您已瞭解如何建立自訂規則和通知，建議的下一個步驟是瞭解如何 [使用自訂分析擴充 Azure IoT Central](howto-create-custom-analytics.md)。
