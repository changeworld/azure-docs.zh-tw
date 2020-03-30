---
title: Azure 信號R 服務的診斷日誌
description: 瞭解如何為 Azure SignalR 服務設置診斷日誌，以及如何利用它進行自故障排除。
author: wanlwanl
ms.service: signalr
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: wanl
ms.openlocfilehash: 72f57ba4bbbbde07f6d26edc88c158f301ebe2f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79536729"
---
# <a name="diagnostic-logs-for-azure-signalr-service"></a>Azure 信號R 服務的診斷日誌

本教程介紹 Azure SignalR 服務的診斷日誌，以及如何設置診斷日誌以及如何使用診斷日誌進行故障排除。

## <a name="prerequisites"></a>Prerequisites
要啟用診斷日誌，您需要在某處存儲日誌資料。 本教程使用 Azure 存儲和日誌分析。

* [Azure 存儲](../azure-monitor/platform/resource-logs-collect-storage.md)- 保留用於策略審核、靜態分析或備份的診斷日誌。
* [日誌分析](../azure-monitor/platform/resource-logs-collect-workspace.md)- 一種靈活的日誌搜索和分析工具，可用於分析 Azure 資源生成的原始日誌。

## <a name="set-up-diagnostic-logs-for-an-azure-signalr-service"></a>為 Azure SignalR 服務設置診斷日誌

您可以查看 Azure 信號R 服務的診斷日誌。 這些日誌提供了與 Azure SignalR 服務實例連接的更豐富的視圖。 診斷日誌提供每個連接的詳細資訊。 例如，連接的基本資訊（使用者 ID、連接 ID 和傳輸類型等）和事件資訊（連接、斷開連接和中止事件等）。 診斷日誌可用於問題識別、連接跟蹤和分析。

### <a name="enable-diagnostic-logs"></a>啟用診斷記錄

診斷記錄預設為停用。 若要啟用診斷記錄，請依照下列步驟操作：

1. 在[Azure 門戶](https://portal.azure.com)中，在 **"監視**"下，按一下 **"診斷設置**"。

    ![窗格導航到診斷設置](./media/signalr-tutorial-diagnostic-logs/diagnostic-settings-menu-item.png)

1. 然後按一下"**添加診斷設置**"。

    ![添加診斷日誌](./media/signalr-tutorial-diagnostic-logs/add-diagnostic-setting.png)

1. 設置所需的存檔目標。 目前，我們支援**存檔到存儲帳戶**和**發送到日誌分析**。

1. 選擇要存檔的日誌。

    ![診斷設定窗格](./media/signalr-tutorial-diagnostic-logs/diagnostics-settings-pane.png)


1. 儲存新的診斷設定。

新的設定大約會在 10 分鐘內生效。 之後，記錄就會在 [診斷記錄]**** 窗格內的已設定封存目標中顯示。

如需設定診斷的詳細資訊，請參閱 [Azure 診斷記錄概觀](../azure-monitor/platform/platform-logs-overview.md)。

### <a name="diagnostic-logs-categories"></a>診斷記錄類別

Azure SignalR 服務捕獲一個類別中的診斷日誌：

* **所有日誌**：跟蹤連接到 Azure SignalR 服務的連接。 日誌提供有關連接/斷開連接、身份驗證和限制的資訊。 如需詳細資訊，請參閱下一節。

### <a name="archive-to-a-storage-account"></a>封存至儲存體帳戶

日誌存儲在**診斷日誌**窗格中配置的存儲帳戶中。 將自動創建名為`insights-logs-alllogs`的容器以存儲診斷日誌。 在容器內，日誌存儲在檔中`resourceId=/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/RESOURCEGROUPS/XXXX/PROVIDERS/MICROSOFT.SIGNALRSERVICE/SIGNALR/XXX/y=YYYY/m=MM/d=DD/h=HH/m=00/PT1H.json`。 基本上，路徑由`resource ID`和`Date Time`合併。 日誌檔由 拆分。 `hour` 因此，分鐘數始終為`m=00`。

所有記錄都會以「JavaScript 物件標記法」(JSON) 格式儲存。 每個項目都具有字串欄位，這些欄位會使用下列小節所述的格式。

存檔日誌 JSON 字串包括下表中列出的元素：

**格式**

名稱 | 描述
------- | -------
time | 日誌事件時間
層級 | 日誌事件級別
resourceId | Azure SignalR 服務的資源識別碼
location | Azure 信號R 服務的位置
category | 日誌事件的類別
operationName | 事件的操作名稱
callerIpAddress | 伺服器/用戶端的 IP 位址
properties | 與此日誌事件相關的詳細屬性。 有關詳細資訊，請參閱下面的屬性工作表

**屬性工作表**

名稱 | 描述
------- | -------
type | 日誌事件的類型。 目前，我們提供有關連接到 Azure SignalR 服務的資訊。 僅`ConnectivityLogs`可用類型
collection | 日誌事件的集合。 允許的值是： `Connection` `Authorization`和`Throttling`
connectionId | 連接的標識
傳輸類型 | 連接的傳輸類型。 允許的值包括： `Websockets` \| `ServerSentEvents` \|`LongPolling`
connectionType | 連線的類型。 允許的值包括：`Server` \| `Client`。 `Server`：從伺服器端連接;`Client`：從用戶端連接
userId | 使用者的身份
message | 日誌事件的詳細消息

下列程式碼是封存記錄 JSON 字串的範例：

```json
{
    "properties": {
        "message": "Entered Serverless mode.",
        "type": "ConnectivityLogs",
        "collection": "Connection",
        "connectionId": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
        "userId": "User",
        "transportType": "WebSockets",
        "connectionType": "Client"
    },
    "operationName": "ServerlessModeEntered",
    "category": "AllLogs",
    "level": "Informational",
    "callerIpAddress": "xxx.xxx.xxx.xxx",
    "time": "2019-01-01T00:00:00Z",
    "resourceId": "/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/RESOURCEGROUPS/XXXX/PROVIDERS/MICROSOFT.SIGNALRSERVICE/SIGNALR/XXX",
    "location": "xxxx"
}
```

### <a name="archive-logs-schema-for-log-analytics"></a>日誌分析的存檔日誌架構

要查看診斷日誌，請按照以下步驟操作：

1. 按一下`Logs`目標日誌分析。

    ![日誌分析功能表項目](./media/signalr-tutorial-diagnostic-logs/log-analytics-menu-item.png)

2. 輸入`SignalRServiceDiagnosticLogs`並選擇時間範圍以查詢診斷日誌。 有關高級查詢，請參閱[Azure 監視器中的日誌分析入門](../azure-monitor/log-query/get-started-portal.md)

    ![日誌分析中的查詢日誌](./media/signalr-tutorial-diagnostic-logs/query-log-in-log-analytics.png)

存檔日誌列包括下表中列出的元素：

名稱 | 描述
------- | ------- 
TimeGenerated | 日誌事件時間
集合 | 日誌事件的集合。 允許的值是： `Connection` `Authorization`和`Throttling`
OperationName | 事件的操作名稱
Location | Azure 信號R 服務的位置
層級 | 日誌事件級別
CallerIpAddress | 伺服器/用戶端的 IP 位址
訊息 | 日誌事件的詳細消息
UserId | 使用者的身份
ConnectionId | 連接的標識
ConnectionType | 連線的類型。 允許的值包括：`Server` \| `Client`。 `Server`：從伺服器端連接;`Client`：從用戶端連接
TransportType | 連接的傳輸類型。 允許的值包括： `Websockets` \| `ServerSentEvents` \|`LongPolling`

### <a name="troubleshooting-with-diagnostic-logs"></a>使用診斷日誌進行故障排除

要對 Azure SignalR 服務進行故障排除，可以啟用伺服器/用戶端日誌來捕獲故障。 目前，Azure SignalR 服務公開診斷日誌，您還可以為服務端啟用日誌。

當遇到連接意外增長或下降的情況時，可以利用診斷日誌進行故障排除。

典型的問題通常是關於連接的意外數量更改、連接達到連接限制和授權失敗的問題。 請參閱有關如何進行故障排除的後續部分。

#### <a name="unexpected-connection-number-changes"></a>意外的連接號碼更改

##### <a name="unexpected-connection-dropping"></a>意外的連接斷開

如果遇到意外的連接丟棄，首先在服務、伺服器和用戶端啟用日誌。

如果連接斷開連接，診斷日誌將記錄此斷開連接事件，您將看到`ConnectionAborted`或`ConnectionEnded`在`operationName`中。

和 之間的區別`ConnectionAborted``ConnectionEnded`是，`ConnectionEnded`這是由用戶端或伺服器端觸發的預期斷開連接。 雖然`ConnectionAborted`通常是意外的連接丟棄事件，並且中止原因將在 中`message`提供。

中止原因列在下表中：

原因 | 描述
------- | ------- 
連接計數達到限制 | 連接計數達到當前價格層的限制。 考慮擴大服務單位
應用程式伺服器關閉連接 | 應用伺服器觸發流產。 它可以被認為是預期流產
連接 ping 超時 | 通常是由網路問題引起的。 考慮從 Internet 檢查應用伺服器的可用性
服務重新載入，重新連接 | Azure 信號R 服務正在重新載入。 Azure SignalR 支援自動重新連接，您可以等到重新連接或手動重新連接到 Azure SignalR 服務
內部伺服器瞬態錯誤 | 瞬態錯誤發生在 Azure SignalR 服務中，應自動復原
伺服器連接已斷開 | 伺服器連接斷開時出現未知錯誤，請考慮先使用服務/伺服器/用戶端日誌進行自故障排除。 嘗試排除基本問題（例如網路問題、應用伺服器端問題等）。 如果問題未解決，請聯繫我們尋求進一步説明。 有關詳細資訊，請參閱[獲取説明](#get-help)部分。 

##### <a name="unexpected-connection-growing"></a>意外的連接增長

要解決意外連接增長的問題，您需要做的第一件事就是篩選出額外的連接。 您可以將唯一的測試使用者 ID 添加到測試用戶端連接。 然後，使用診斷日誌進行驗證，如果您看到多個用戶端連接具有相同的測試使用者 ID 或 IP，則用戶端可能創建並建立比預期更多的連接。 檢查用戶端。

#### <a name="authorization-failure"></a>授權失敗

如果收到 401 個未授權返回的用戶端請求，請檢查診斷日誌。 如果遇到`Failed to validate audience. Expected Audiences: <valid audience>. Actual Audiences: <actual audience>`，則表示訪問權杖中的所有訪問群體都無效。 嘗試使用日誌中建議的有效訪問群體。


#### <a name="throttling"></a>節流

如果發現無法建立與 Azure SignalR 服務的 SignalR 用戶端連接，請檢查診斷日誌。 如果在診斷日誌`Connection count reaches limit`中遇到，則與 SignalR 服務建立的連接過多，該連接達到連接計數限制。 請考慮擴展 SignalR 服務。 如果在診斷日誌`Message count reaches limit`中遇到，則意味著使用免費層，並且使用消息的配額。 如果要發送更多消息，請考慮將 SignalR 服務更改為標準層以發送其他消息。 有關詳細資訊，請參閱[Azure SignalR 服務定價](https://azure.microsoft.com/pricing/details/signalr-service/)。

### <a name="get-help"></a>取得說明

我們建議您先自行疑難排解。 大多數問題是由應用伺服器或網路問題引起的。 使用[診斷日誌](#troubleshooting-with-diagnostic-logs)和[基本故障拍攝指南](https://github.com/Azure/azure-signalr/blob/dev/docs/tsg.md)遵循故障排除指南，查找根本原因。
如果問題仍無法解決，請考慮在 GitHub 中打開問題或在 Azure 門戶中創建票證。
提供：
1. 出現問題時的時間範圍約為 30 分鐘
2. Azure SignalR 服務的資源識別碼
3. 發佈詳細資訊，盡可能具體：例如，應用程式伺服器不發送消息、用戶端連接斷開等
4. 從伺服器/用戶端收集的日誌和其他可能有用的材料
5. [可選]重現代碼

> [!NOTE]
> 如果在 GitHub 中打開問題，請保持敏感資訊（例如資源識別碼、伺服器/用戶端日誌）的私密性，僅私下發送給 Microsoft 組織中的成員。