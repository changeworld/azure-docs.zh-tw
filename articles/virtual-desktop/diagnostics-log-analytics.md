---
title: Windows 虛擬桌面診斷記錄分析-Azure
description: 如何搭配使用 log analytics 與 Windows 虛擬桌面診斷功能。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 05/27/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 7a138308b48a24a78c55bdc0105379e31482456d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85209380"
---
# <a name="use-log-analytics-for-the-diagnostics-feature"></a>針對診斷功能使用 Log Analytics

>[!IMPORTANT]
>此內容適用於具有 Azure Resource Manager Windows 虛擬桌面物件的 2020 年春季更新版。 如果您使用不含 Azure Resource Manager 物件的 Windows 虛擬桌面 2019 年秋季版，請參閱[這篇文章](./virtual-desktop-fall-2019/diagnostics-log-analytics-2019.md)。
>
> Windows 虛擬桌面 2020 年春季更新版目前為公開預覽狀態。 此預覽版本是在沒有服務等級協定的情況下提供，不建議您將其用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

Windows 虛擬桌面使用[Azure 監視器](../azure-monitor/overview.md)來監視和警示，就像許多其他 Azure 服務一樣。 這可讓系統管理員透過單一介面來識別問題。 服務會為使用者和系統管理動作建立活動記錄。 每個活動記錄都落在下列類別之下：

- 管理活動：
    - 追蹤是否嘗試使用 Api 或 PowerShell 來變更 Windows 虛擬桌面物件是成功的。 例如，有人可以使用 PowerShell 成功建立主機集區嗎？
- 摘要
    - 使用者是否可以成功訂閱工作區？
    - 使用者是否會看到所有在遠端桌面用戶端發佈的資源？
- 連線:
    - 當使用者起始並完成服務的連接時。
- 主機註冊：
    - 工作階段主機是否已在連線時成功向服務註冊？
- Errors：
    - 使用者是否遇到特定活動的任何問題？ 只要資訊與活動聯結，這項功能就可以產生一個追蹤活動資料的資料表。
- 把
    - 已達到活動存留期的特定步驟。 例如，在會話期間，使用者已負載平衡至特定主機，然後使用者在連線期間登入，依此類推。

因為診斷角色服務本身是 Windows 虛擬桌面的一部分，所以在診斷結果中不會顯示未觸達 Windows 虛擬桌面的連線。 當使用者遇到網路連線問題時，會發生 Windows 虛擬桌面連接問題。

Azure 監視器可讓您分析 Windows 虛擬桌面的資料，並查看虛擬機器（VM）效能計數器，全部都在相同的工具中。 本文將告訴您如何為您的 Windows 虛擬桌面環境啟用診斷功能。

>[!NOTE]
>若要瞭解如何在 Azure 中監視您的 Vm，請參閱[使用 Azure 監視器監視 azure 虛擬機器](../azure-monitor/insights/monitor-vm-azure.md)。 此外，請務必[參閱效能計數器閾值](../virtual-desktop/virtual-desktop-fall-2019/deploy-diagnostics.md#windows-performance-counter-thresholds)，以進一步瞭解您在工作階段主機上的使用者體驗。

## <a name="before-you-get-started"></a>開始之前

您必須先建立工作區，才可以使用 Log Analytics。 若要這麼做，請遵循下列兩篇文章中的指示進行：

- 如果您偏好使用 Azure 入口網站，請參閱[在 Azure 入口網站中建立 Log Analytics 工作區](../azure-monitor/learn/quick-create-workspace.md)。
- 如果您偏好 PowerShell，請參閱[使用 Powershell 建立 Log Analytics 工作區](../azure-monitor/learn/quick-create-workspace-posh.md)。

建立工作區之後，請依照[將 Windows 電腦連線至 Azure 監視器](../azure-monitor/platform/agent-windows.md#obtain-workspace-id-and-key)中的指示取得下列資訊：

- 工作區識別碼
- 工作區的主要金鑰

您稍後會在安裝過程中需要此資訊。

請務必審查 Azure 監視器的版權管理，以針對監視和維護您的 Windows 虛擬桌面環境的人員啟用資料存取。 如需詳細資訊，請參閱以[Azure 監視器開始使用角色、許可權和安全性](../azure-monitor/platform/roles-permissions-security.md)。

## <a name="push-diagnostics-data-to-your-workspace"></a>將診斷資料推送至您的工作區

您可以將診斷資料從您的 Windows 虛擬桌面物件推送至工作區的 Log Analytics。 當您第一次建立物件時，可以立即設定這項功能。

若要設定新物件的 Log Analytics：

1. 登入 Azure 入口網站並移至**Windows 虛擬桌面**。

2. 流覽至您想要為其捕獲記錄和事件的物件（例如主機集區、應用程式群組或工作區）。

3. 選取畫面左側功能表中的 [**診斷設定**]。

4. 在畫面右側出現的功能表中，選取 [**新增診斷設定**]。

    [診斷設定] 頁面中顯示的選項會根據您要編輯的物件種類而有所不同。

    例如，當您啟用應用程式群組的診斷功能時，您會看到設定檢查點、錯誤和管理的選項。 針對工作區，這些類別會設定摘要來追蹤使用者訂閱應用程式清單的時間。 若要深入瞭解診斷設定，請參閱[建立診斷設定以收集 Azure 中的資源記錄和計量](../azure-monitor/platform/diagnostic-settings.md)。

     >[!IMPORTANT]
     >請記得針對您想要監視的每個 Azure Resource Manager 物件啟用診斷。 啟用診斷之後，資料將可供活動使用。 第一次設定之後，可能需要幾個小時的時間。

5. 輸入設定的名稱，然後選取 [**傳送至 Log Analytics**]。 您使用的名稱不應該有空格，且應符合[Azure 命名慣例](../azure-resource-manager/management/resource-name-rules.md)。 在記錄中，您可以選取您想要新增到 Log Analytics 的所有選項，例如檢查點、錯誤、管理等。

6. 選取 [儲存]。

>[!NOTE]
>Log Analytics 可讓您選擇將資料串流至[事件中樞](../event-hubs/event-hubs-about.md)，或將它封存在儲存體帳戶中。 若要深入瞭解這項功能，請參閱將[azure 監視資料串流至事件中樞](../azure-monitor/platform/stream-monitoring-data-event-hubs.md)，並[將 azure 資源記錄封存至儲存體帳戶](../azure-monitor/platform/resource-logs-collect-storage.md)。

## <a name="how-to-access-log-analytics"></a>如何存取 Log Analytics

您可以在 Azure 入口網站或 Azure 監視器上存取 Log Analytics 工作區。

### <a name="access-log-analytics-on-a-log-analytics-workspace"></a>存取 Log Analytics 工作區上的 Log Analytics

1. 登入 Azure 入口網站。

2. 搜尋**Log Analytics 工作區**。

3. 在 [服務] 下，選取 [ **Log Analytics 工作區**]。

4. 從清單中，選取您為 Windows 虛擬桌面物件設定的工作區。

5. 在您的工作區中，選取 [**記錄**]。 您可以使用**搜尋**功能來篩選您的功能表清單。

### <a name="access-log-analytics-on-azure-monitor"></a>存取 Azure 監視器上的 Log Analytics

1. 登入 Azure 入口網站

2. 搜尋並選取 [監視]。

3. 選取 [記錄]。

4. 依照 [記錄] 頁面中的指示來設定查詢的範圍。

5. 您已準備好查詢診斷。 所有診斷資料表都有 "WVD" 前置詞。

>[!NOTE]
>如需有關 Azure 監視器記錄中所儲存之資料表的詳細資訊，請參閱[Azure 監視器資料參考](https://docs.microsoft.com/azure/azure-monitor/reference/)。 與 Windows 虛擬桌面相關的所有資料表都會標示為「WVD」。

## <a name="cadence-for-sending-diagnostic-events"></a>傳送診斷事件的步調

完成時，會將診斷事件傳送至 Log Analytics。

Log Analytics 只會報告下列連接活動的中繼狀態：

- 已啟動：當使用者選取並聯機到遠端桌面用戶端中的應用程式或桌面時。
- 已連線：當使用者成功連線至裝載應用程式或桌面的 VM 時。
- 已完成：當使用者或伺服器中斷連線會話時，活動就會發生在中。

## <a name="example-queries"></a>查詢範例

下列查詢範例顯示診斷功能如何為系統中最常用的活動產生報告。

若要取得使用者所建立的連線清單，請執行此 Cmdlet：

```kusto
WVDConnections
| project-away TenantId,SourceSystem
| summarize arg_max(TimeGenerated, *), StartTime =  min(iff(State== 'Started', TimeGenerated , datetime(null) )), ConnectTime = min(iff(State== 'Connected', TimeGenerated , datetime(null) ))   by CorrelationId
| join kind=leftouter (
    WVDErrors
    |summarize Errors=makelist(pack('Code', Code, 'CodeSymbolic', CodeSymbolic, 'Time', TimeGenerated, 'Message', Message ,'ServiceError', ServiceError, 'Source', Source)) by CorrelationId
    ) on CorrelationId
| join kind=leftouter (
   WVDCheckpoints
   | summarize Checkpoints=makelist(pack('Time', TimeGenerated, 'Name', Name, 'Parameters', Parameters, 'Source', Source)) by CorrelationId
   | mv-apply Checkpoints on
    (
        order by todatetime(Checkpoints['Time']) asc
        | summarize Checkpoints=makelist(Checkpoints)
    )
   ) on CorrelationId
| project-away CorrelationId1, CorrelationId2
| order by  TimeGenerated desc
```

若要查看使用者的摘要活動：

```kusto
WVDFeeds
| project-away TenantId,SourceSystem
| join kind=leftouter (
    WVDErrors
    |summarize Errors=makelist(pack('Code', Code, 'CodeSymbolic', CodeSymbolic, 'Time', TimeGenerated, 'Message', Message ,'ServiceError', ServiceError, 'Source', Source)) by CorrelationId
    ) on CorrelationId
| join kind=leftouter (
   WVDCheckpoints
   | summarize Checkpoints=makelist(pack('Time', TimeGenerated, 'Name', Name, 'Parameters', Parameters, 'Source', Source)) by CorrelationId
   | mv-apply Checkpoints on
    (
        order by todatetime(Checkpoints['Time']) asc
        | summarize Checkpoints=makelist(Checkpoints)
    )
   ) on CorrelationId
| project-away CorrelationId1, CorrelationId2
| order by  TimeGenerated desc
```

若要尋找單一使用者的所有連接：

```kusto
WVDConnections
|where UserName == "userupn"
|take 100
|sort by TimeGenerated asc, CorrelationId
```


若要尋找使用者每天連線的次數：

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

若要找出是否發生特定的錯誤：

```kusto
WVDErrors
| where CodeSymbolic =="ErrorSymbolicCode"
| summarize count(UserName) by CodeSymbolic
```

若要尋找所有使用者的錯誤發生：

```kusto
WVDErrors
| where ServiceError =="false"
| summarize usercount = count(UserName) by CodeSymbolic
| sort by usercount desc
| render barchart
```

若要查詢使用者已開啟的應用程式，請執行此查詢：

```kusto
WVDCheckpoints
| where TimeGenerated > ago(7d)
| where Name == "LaunchExecutable"
| extend App = parse_json(Parameters).filename
| summarize Usage=count(UserName) by tostring(App)
| sort by Usage desc
| render columnchart
```
>[!NOTE]
>- 當使用者開啟完整桌面時，其在會話中的應用程式使用量不會在 WVDCheckpoints 資料表中追蹤為檢查點。
>- [WVDConnections] 資料表中的 [ResourcesAlias] 資料行會顯示使用者是否已連線至完整桌面或已發佈的應用程式。 此資料行只會顯示其在連線期間開啟的第一個應用程式。 使用者開啟的任何已發佈應用程式都會在 WVDCheckpoints 中進行追蹤。
>- WVDErrors 資料表會顯示管理錯誤、主機註冊問題，以及使用者訂閱應用程式或桌上型電腦清單時所發生的其他問題。
>- WVDErrors 可協助您找出可由管理工作解決的問題。 對於這些類型的問題，ServiceError 上的值一律會顯示 "false"。 如果 ServiceError = "true"，您必須將問題呈報給 Microsoft。 請確定您為呈報的錯誤提供了 CorrelationID。

## <a name="next-steps"></a>後續步驟

若要查看診斷功能可為您識別的常見錯誤案例，請參閱[識別並診斷問題](diagnostics-role-service.md#common-error-scenarios)。
