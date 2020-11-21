---
title: Windows 虛擬桌面診斷記錄分析-Azure
description: 如何搭配使用 log analytics 與 Windows 虛擬桌面診斷功能。
author: Heidilohr
ms.topic: how-to
ms.date: 05/27/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 5d8b696b175c4ef841eef1a51f1d357d1781cba7
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/21/2020
ms.locfileid: "95018285"
---
# <a name="use-log-analytics-for-the-diagnostics-feature"></a>使用 Log Analytics 進行診斷功能

>[!IMPORTANT]
>此內容適用於具有 Azure Resource Manager Windows 虛擬桌面物件的 Windows 虛擬桌面。 如果您使用不含 Azure Resource Manager 物件的 Windows 虛擬桌面 (傳統)，請參閱[此文章](./virtual-desktop-fall-2019/diagnostics-log-analytics-2019.md)。

Windows 虛擬桌面使用 [Azure 監視器](../azure-monitor/overview.md) 來監視和警示，就像許多其他 Azure 服務一樣。 系統管理員能藉此透過單一介面來辨識問題。 此服務會建立使用者和系統管理動作的活動記錄。 每個活動記錄都屬於下列類別：

- 管理活動：
    - 追蹤使用 Api 或 PowerShell 來變更 Windows 虛擬桌面物件的嘗試是否成功。 例如，某人是否可以使用 PowerShell 成功建立主機集區？
- 飼料：
    - 使用者可以成功訂閱工作區嗎？
    - 使用者是否會看到在遠端桌面用戶端中發佈的所有資源？
- 連線:
    - 當使用者起始並完成服務的連接時。
- 主機註冊：
    - 工作階段主機是否已在連接時成功向服務註冊？
- Errors：
    - 使用者是否遇到任何特定活動的問題？ 只要資訊與活動聯結，這項功能就會產生一個可追蹤活動資料的資料表。
- 檢查站：
    - 已達到活動存留期內的特定步驟。 例如，在會話期間，使用者已將負載平衡至特定主機，然後使用者在連線期間登入，依此類推。

因為診斷角色服務本身是 Windows 虛擬桌面的一部分，所以在診斷結果中不會顯示未觸達 Windows 虛擬桌面的連線。 當使用者遇到網路連線問題時，可能會發生 Windows 虛擬桌面連線問題。

Azure 監視器可讓您分析 Windows 虛擬桌面資料，並在相同的工具中查看虛擬機器 (VM) 效能計數器。 本文將告訴您如何為您的 Windows 虛擬桌面環境啟用診斷功能。

>[!NOTE]
>若要瞭解如何在 Azure 中監視 Vm，請參閱 [使用 Azure 監視器監視 azure 虛擬機器](../azure-monitor/insights/monitor-vm-azure.md)。 此外，請務必 [檢查效能計數器閾值](../virtual-desktop/virtual-desktop-fall-2019/deploy-diagnostics.md#windows-performance-counter-thresholds) ，以進一步瞭解您在工作階段主機上的使用者體驗。

## <a name="before-you-get-started"></a>開始之前

在您可以使用 Log Analytics 之前，您必須先建立工作區。 若要這樣做，請遵循下列其中一篇文章中的指示：

- 如果您偏好使用 Azure 入口網站，請參閱 [Azure 入口網站中的建立 Log Analytics 工作區](../azure-monitor/learn/quick-create-workspace.md)。
- 如果您偏好使用 PowerShell，請參閱 [使用 Powershell 建立 Log Analytics 工作區](../azure-monitor/platform/powershell-workspace-configuration.md)。

建立工作區之後，請依照 [將 Windows 電腦連線到 Azure 監視器](../azure-monitor/platform/log-analytics-agent.md#workspace-id-and-key) 中的指示取得下列資訊：

- 工作區識別碼
- 您工作區的主要金鑰

您稍後會在安裝程式中需要此資訊。

請務必審核 Azure 監視器的版權管理，以針對監視和維護您的 Windows 虛擬桌面環境的人員啟用資料存取。 如需詳細資訊，請參閱 [Azure 監視器的角色、許可權和安全性](../azure-monitor/platform/roles-permissions-security.md)入門。

## <a name="push-diagnostics-data-to-your-workspace"></a>將診斷資料推送至您的工作區

您可以將診斷資料從您的 Windows 虛擬桌面物件推送至適用于您工作區的 Log Analytics。 當您第一次建立物件時，可以立即設定這項功能。

若要設定新物件的 Log Analytics：

1. 登入 Azure 入口網站並移至 [ **Windows 虛擬桌面**]。

2. 流覽至您要為其捕獲記錄和事件的物件 (例如主機集區、應用程式群組或工作區) 。

3. 在畫面左側的功能表中，選取 [ **診斷設定** ]。

4. 選取出現在畫面右側功能表中的 [ **新增診斷設定** ]。

    [診斷設定] 頁面中顯示的選項會根據您要編輯的物件類型而有所不同。

    例如，當您為應用程式群組啟用診斷時，您會看到設定檢查點、錯誤和管理的選項。 針對工作區，這些類別會設定摘要，以在使用者訂閱應用程式清單時追蹤。 若要深入瞭解診斷設定，請參閱 [建立診斷設定以收集 Azure 中的資源記錄和計量](../azure-monitor/platform/diagnostic-settings.md)。

     >[!IMPORTANT]
     >請記得針對您要監視的每個 Azure Resource Manager 物件啟用診斷。 啟用診斷之後，會有活動可用的資料。 第一次設定之後，可能需要幾個小時的時間。

5. 輸入設定設定的名稱，然後選取 [ **傳送至 Log Analytics**]。 您使用的名稱不應包含空格，而且應該符合 [Azure 命名慣例](../azure-resource-manager/management/resource-name-rules.md)。 在記錄中，您可以選取您要新增至 Log Analytics 的所有選項，例如檢查點、錯誤、管理等等。

6. 選取 [儲存]。

>[!NOTE]
>Log Analytics 可讓您選擇將資料串流至 [事件中樞](../event-hubs/event-hubs-about.md) ，或將它保存在儲存體帳戶中。 若要深入瞭解這項功能，請參閱將 [azure 監視資料串流至事件中樞](../azure-monitor/platform/stream-monitoring-data-event-hubs.md) ，並 [將 azure 資源記錄封存至儲存體帳戶](../azure-monitor/platform/resource-logs.md#send-to-azure-storage)。

## <a name="how-to-access-log-analytics"></a>如何存取 Log Analytics

您可以在 Azure 入口網站或 Azure 監視器上存取 Log Analytics 工作區。

### <a name="access-log-analytics-on-a-log-analytics-workspace"></a>存取 Log Analytics 工作區上的 Log Analytics

1. 登入 Azure 入口網站。

2. 搜尋 **Log Analytics 工作區**。

3. 在 [服務] 下，選取 [ **Log Analytics 工作區**]。

4. 從清單中選取您為 Windows 虛擬桌面物件設定的工作區。

5. 在您的工作區中，選取 [ **記錄**]。 您可以使用 **搜尋** 功能來篩選出功能表清單。

### <a name="access-log-analytics-on-azure-monitor"></a>存取 Azure 監視器上的 Log Analytics

1. 登入 Azure 入口網站

2. 搜尋並選取 [監視]。

3. 選取 [記錄]  。

4. 遵循 [記錄] 頁面中的指示來設定您的查詢範圍。

5. 您已經準備好查詢診斷。 所有診斷資料表都有一個 "WVD" 前置詞。

>[!NOTE]
>如需有關 Azure 監視器記錄檔中所儲存之資料表的詳細資訊，請參閱 [Azure 監視器資料參考](/azure/azure-monitor/reference/)。 與 Windows 虛擬桌面相關的所有資料表都會標示為「WVD」。

## <a name="cadence-for-sending-diagnostic-events"></a>傳送診斷事件的步調

診斷事件會在完成時傳送至 Log Analytics。

Log Analytics 只會報告這些連接活動的中繼狀態：

- 已啟動：當使用者選取並聯機到遠端桌面用戶端中的應用程式或桌面。
- 已連線：當使用者成功連接到裝載應用程式或桌面的 VM 時。
- 已完成：當使用者或伺服器中斷活動發生的會話時。

## <a name="example-queries"></a>查詢範例

透過 Azure 監視器 Log Analytics UI 存取查詢範例：
1. 移至您的 Log Analytics 工作區，然後選取 [ **記錄**]。 範例查詢 UI 會自動顯示。
1. 將篩選準則變更為 **Category**。
1. 選取 [ **Windows 虛擬桌面** ] 以查看可用的查詢。
1. 選取 [ **執行** ] 以執行選取的查詢。

深入瞭解 [Azure 監視器 Log Analytics 中儲存查詢](../azure-monitor/log-query/example-queries.md)的範例查詢介面。

下列查詢清單可讓您查看單一使用者的連接資訊或問題。 您可以在 [Log Analytics 查詢編輯器](../azure-monitor/log-query/log-analytics-tutorial.md#write-a-query)中執行這些查詢。 針對每個查詢，將取代為 `userupn` 您要查閱之使用者的 UPN。


若要尋找單一使用者的所有連接：

```kusto
WVDConnections
|where UserName == "userupn"
|take 100
|sort by TimeGenerated asc, CorrelationId
```


若要找出使用者每天連接的次數：

```kusto
WVDConnections
|where UserName == "userupn"
|take 100
|sort by TimeGenerated asc, CorrelationId
|summarize dcount(CorrelationId) by bin(TimeGenerated, 1d)
```

若要依使用者尋找會話持續時間：

```kusto
let Events = WVDConnections | where UserName == "userupn" ;
Events
| where State == "Connected"
| project CorrelationId , UserName, ResourceAlias , StartTime=TimeGenerated
| join (Events
| where State == "Completed"
| project EndTime=TimeGenerated, CorrelationId)
on CorrelationId
| project Duration = EndTime - StartTime, ResourceAlias
| sort by Duration asc
```

若要尋找特定使用者的錯誤：

```kusto
WVDErrors
| where UserName == "userupn"
|take 100
```

若要瞭解是否有其他使用者發生特定的錯誤：

```kusto
WVDErrors
| where CodeSymbolic =="ErrorSymbolicCode"
| summarize count(UserName) by CodeSymbolic
```


>[!NOTE]
>- 當使用者開啟完整桌面時，會話中的應用程式使用方式不會在 WVDCheckpoints 資料表中視為檢查點來追蹤。
>- WVDConnections 資料表中的 ResourcesAlias 資料行會顯示使用者是否已連接至完整桌面或已發佈的應用程式。 此資料行只會顯示在連接期間開啟的第一個應用程式。 使用者開啟的任何已發佈應用程式都會在 WVDCheckpoints 中追蹤。
>- WVDErrors 資料表顯示當使用者訂閱應用程式或桌上型電腦清單時，所發生的管理錯誤、主機註冊問題和其他問題。
>- WVDErrors 可協助您找出可由管理工作解決的問題。 針對這些類型的問題，ServiceError 上的值一律會顯示為 "false"。 如果 ServiceError = "true"，您將需要將問題呈報給 Microsoft。 請確定您提供了您呈報的錯誤 CorrelationID。

## <a name="next-steps"></a>下一步

若要檢查診斷功能可為您識別的常見錯誤案例，請參閱 [識別及診斷問題](diagnostics-role-service.md#common-error-scenarios)。