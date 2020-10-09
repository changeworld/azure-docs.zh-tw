---
title: Azure SignalR Service 的資源記錄
description: 瞭解如何設定 Azure SignalR Service 的資源記錄，以及如何利用它來自行疑難排解。
author: wanlwanl
ms.service: signalr
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: wanl
ms.openlocfilehash: 55482457058d01162116494b637661db40010a50
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "85131958"
---
# <a name="resource-logs-for-azure-signalr-service"></a>Azure SignalR Service 的資源記錄

本教學課程會討論 Azure SignalR Service 的資源記錄檔、如何設定它們，以及如何進行疑難排解。 

## <a name="prerequisites"></a>必要條件
若要啟用資源記錄，您將需要儲存記錄資料的地方。 本教學課程使用 Azure 儲存體和 Log Analytics。

* [Azure 儲存體](../azure-monitor/platform/resource-logs-collect-storage.md) -保留原則審核、靜態分析或備份的資源記錄。
* [Log Analytics](../azure-monitor/platform/resource-logs-collect-workspace.md) -彈性的記錄搜尋和分析工具，可讓您分析 Azure 資源所產生的原始記錄。

## <a name="set-up-resource-logs-for-an-azure-signalr-service"></a>設定 Azure SignalR Service 的資源記錄

您可以查看 Azure SignalR Service 的資源記錄。 這些記錄可讓您更深入瞭解您的 Azure SignalR Service 實例的連接。 資源記錄會提供每個連接的詳細資訊。 例如，基本資訊 (使用者識別碼、連接識別碼和傳輸類型等等) 和事件資訊 (連接、中斷連線和中止事件，以及連線) 等。 資源記錄可用於問題識別、連接追蹤和分析。

### <a name="enable-resource-logs"></a>啟用資源記錄

預設會停用資源記錄。 若要啟用資源記錄檔，請遵循下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com)的 [ **監視**] 底下，按一下 [ **診斷設定**]。

    ![診斷設定的窗格流覽](./media/signalr-tutorial-diagnostic-logs/diagnostic-settings-menu-item.png)

1. 然後按一下 [ **新增診斷設定**]。

    ![新增資源記錄](./media/signalr-tutorial-diagnostic-logs/add-diagnostic-setting.png)

1. 設定您想要的保存目標。 目前，我們支援封存 **至儲存體帳戶** 並 **傳送至 Log Analytics**。

1. 選取您要封存的記錄檔。

    ![診斷設定窗格](./media/signalr-tutorial-diagnostic-logs/diagnostics-settings-pane.png)


1. 儲存新的診斷設定。

新的設定大約會在 10 分鐘內生效。 之後，記錄就會在 [診斷記錄] 窗格內的已設定封存目標中顯示。

如需有關設定診斷的詳細資訊，請參閱 [Azure 資源記錄的總覽](../azure-monitor/platform/platform-logs-overview.md)。

### <a name="resource-logs-categories"></a>資源記錄類別

Azure SignalR Service 會在一個類別中捕捉資源記錄：

* **所有記錄**：追蹤連接到 Azure SignalR Service 的連接。 記錄檔會提供連接/中斷連線、驗證和節流的相關資訊。 如需詳細資訊，請參閱下一節。

### <a name="archive-to-a-storage-account"></a>封存至儲存體帳戶

記錄會儲存在 [ **診斷記錄** ] 窗格中設定的儲存體帳戶中。 `insights-logs-alllogs`系統會自動建立名為的容器來儲存資源記錄。 在容器內，記錄檔會儲存在檔案中 `resourceId=/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/RESOURCEGROUPS/XXXX/PROVIDERS/MICROSOFT.SIGNALRSERVICE/SIGNALR/XXX/y=YYYY/m=MM/d=DD/h=HH/m=00/PT1H.json` 。 基本上，路徑是由和所 `resource ID` 結合 `Date Time` 。 記錄檔是由分割 `hour` 。 因此，分鐘數一律為 `m=00` 。

所有記錄都會以「JavaScript 物件標記法」(JSON) 格式儲存。 每個項目都具有字串欄位，這些欄位會使用下列小節所述的格式。

封存記錄檔 JSON 字串包括下表所列的元素：

**格式**

名稱 | 描述
------- | -------
time | 記錄檔事件時間
等級 | 記錄事件層級
resourceId | Azure SignalR Service 的資源識別碼
location | Azure SignalR Service 的位置
category | 記錄檔事件的類別
operationName | 事件的作業名稱
callerIpAddress | 您伺服器/用戶端的 IP 位址
properties | 與此記錄事件相關的詳細屬性。 如需詳細資訊，請參閱下表中的屬性工作表

**Properties 資料表**

名稱 | 描述
------- | -------
type | 記錄檔事件的類型。 目前，我們會提供與 Azure SignalR Service 的連接相關資訊。 只有 `ConnectivityLogs` 類型可供使用
collection | 記錄檔事件的集合。 允許的值為 `Connection` ： `Authorization` 和。 `Throttling`
connectionId | 連接的身分識別
transportType | 連接的傳輸類型。 允許的值為： `Websockets` \| `ServerSentEvents` \|`LongPolling`
connectionType | 連線的類型。 允許的值包括：`Server` \| `Client`。 `Server`：伺服器端的連接; `Client`：來自用戶端的連接
userId | 使用者的身分識別
訊息 | 記錄事件的詳細訊息

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

### <a name="archive-logs-schema-for-log-analytics"></a>Log Analytics 的封存記錄架構

若要查看資源記錄檔，請遵循下列步驟：

1. 按一下 `Logs` 您的目標記錄分析。

    ![Log Analytics 功能表項目](./media/signalr-tutorial-diagnostic-logs/log-analytics-menu-item.png)

2. 輸入 `SignalRServiceDiagnosticLogs` 並選取時間範圍，以查詢資源記錄。 如需 advanced query，請參閱 [Azure 監視器中的開始使用 Log Analytics](../azure-monitor/log-query/get-started-portal.md)

    ![Log Analytics 中的查詢記錄](./media/signalr-tutorial-diagnostic-logs/query-log-in-log-analytics.png)

封存記錄資料行包含下表所列的元素：

名稱 | 描述
------- | ------- 
TimeGenerated | 記錄檔事件時間
集合 | 記錄檔事件的集合。 允許的值為 `Connection` ： `Authorization` 和。 `Throttling`
OperationName | 事件的作業名稱
Location | Azure SignalR Service 的位置
層級 | 記錄事件層級
CallerIpAddress | 您伺服器/用戶端的 IP 位址
訊息 | 記錄事件的詳細訊息
UserId | 使用者的身分識別
ConnectionId | 連接的身分識別
ConnectionType | 連線的類型。 允許的值包括：`Server` \| `Client`。 `Server`：伺服器端的連接; `Client`：來自用戶端的連接
TransportType | 連接的傳輸類型。 允許的值為： `Websockets` \| `ServerSentEvents` \|`LongPolling`

### <a name="troubleshooting-with-resource-logs"></a>使用資源記錄進行疑難排解

若要針對 Azure SignalR Service 進行疑難排解，您可以啟用伺服器/用戶端記錄檔來捕捉失敗。 目前，Azure SignalR Service 公開資源記錄檔，您也可以啟用服務端的記錄檔。

遇到連線非預期的成長或捨棄情況時，您可以利用資源記錄來進行疑難排解。

一般問題通常是關於連接的非預期數量變更、連線達到連線限制和授權失敗。 請參閱下一節，以瞭解如何進行疑難排解。

#### <a name="unexpected-connection-number-changes"></a>未預期的連接號碼變更

##### <a name="unexpected-connection-dropping"></a>未預期的連接卸載

如果您遇到非預期的連接，請先在服務、伺服器和用戶端啟用記錄。

如果連接中斷，資源記錄檔將會記錄此中斷線上活動，您將會看到 `ConnectionAborted` 或 `ConnectionEnded` `operationName` 。

和之間的差異，是 `ConnectionAborted` `ConnectionEnded` `ConnectionEnded` 用戶端或伺服器端所觸發的預期中斷連接。 雖然 `ConnectionAborted` 通常是未預期的連接卸載事件，而且會在中提供中止原因 `message` 。

下表列出中止的原因：

原因 | 描述
------- | ------- 
連接計數達到限制 | 連接計數達到您目前定價層的限制。 考慮擴大服務單位
應用程式伺服器已關閉連接 | 應用程式伺服器會觸發 abortion。 它可以視為預期的 abortion
連接偵測超時 | 通常是因為網路問題所造成。 請考慮從網際網路檢查應用程式伺服器的可用性
服務重載，重新連接 | Azure SignalR Service 正在重載。 Azure SignalR 支援自動重新連接，您可以等到重新連線或手動重新連接到 Azure SignalR Service
內部伺服器暫時性錯誤 | Azure SignalR Service 中發生暫時性錯誤，應自動復原
伺服器連接中斷 | 伺服器連接中斷，但發生未知的錯誤，請考慮以服務/伺服器/用戶端記錄檔的自我疑難排解。 請嘗試排除基本問題 (例如網路問題、應用程式伺服器端問題等) 。 如果問題未解決，請與我們聯繫以取得進一步的協助。 如需詳細資訊，請參閱 [取得](#get-help) 說明區段。 

##### <a name="unexpected-connection-growing"></a>未預期的連接成長

若要針對非預期的連接增加進行疑難排解，您必須做的第一件事是篩選掉額外的連接。 您可以將唯一的測試使用者識別碼加入至測試用戶端連接。 然後使用資源記錄進行驗證，如果您看到一個以上的用戶端連線具有相同的測試使用者識別碼或 IP，則用戶端可能會建立和建立比預期更多的連線。 檢查您的用戶端。

#### <a name="authorization-failure"></a>授權失敗

如果您在用戶端要求中收到401的未經授權傳回，請檢查您的資源記錄。 如果您遇到 `Failed to validate audience. Expected Audiences: <valid audience>. Actual Audiences: <actual audience>` 這種情況，這表示您存取權杖中的所有物件都是不正確。 請嘗試使用記錄檔中建議的有效物件。


#### <a name="throttling"></a>節流

如果您發現無法 Azure SignalR Service 建立 SignalR 用戶端連線，請檢查您的資源記錄。 如果您 `Connection count reaches limit` 在資源記錄檔中遇到，則會建立太多與 SignalR Service 的連線，以達到連接計數限制。 請考慮擴大您的 SignalR Service。 如果您 `Message count reaches limit` 在資源記錄檔中遇到，這表示您使用的是免費層，且您使用的是訊息配額。 如果您想要傳送更多訊息，請考慮將您的 SignalR Service 變更為標準層，以傳送額外的訊息。 如需詳細資訊，請參閱 [Azure SignalR Service 定價](https://azure.microsoft.com/pricing/details/signalr-service/)。

### <a name="get-help"></a>取得協助

我們建議您先自行進行疑難排解。 大部分的問題都是由應用程式伺服器或網路問題所造成。 請遵循資源記錄檔和[基本](https://github.com/Azure/azure-signalr/blob/dev/docs/tsg.md)疑難排解指南中的[疑難排解指南](#troubleshooting-with-resource-logs)，找出根本原因。
如果問題仍無法解決，請考慮在 GitHub 中開啟問題，或在 Azure 入口網站中建立票證。
提供：
1. 發生問題的時間範圍大約30分鐘
2. Azure SignalR Service 的資源識別碼
3. 盡可能明確地發出詳細資料：例如，>appserver 不會傳送訊息、用戶端連接中斷等等
4. 從伺服器/用戶端收集的記錄，以及其他可能有用的材質
5. 參數重現程式碼

> [!NOTE]
> 如果您在 GitHub 中開啟問題，請將您的機密資訊 (例如，資源識別碼、伺服器/用戶端記錄) 私用，只會私下傳送給 Microsoft 組織中的成員。