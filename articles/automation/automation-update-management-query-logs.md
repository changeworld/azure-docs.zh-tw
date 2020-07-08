---
title: 查詢 Azure 自動化更新管理記錄
description: 本文說明如何在 Log Analytics 工作區中查詢更新管理的記錄。
services: automation
ms.subservice: update-management
ms.date: 04/06/2020
ms.topic: conceptual
ms.openlocfilehash: b40357e71275d835a200f3bc08c618b6713001d8
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.contentlocale: zh-TW
ms.lasthandoff: 05/25/2020
ms.locfileid: "83830764"
---
# <a name="query-update-management-logs"></a>查詢更新管理記錄

除了更新管理部署期間所提供的詳細資料之外，您還可搜尋 Log Analytics 工作區中儲存的記錄。 若要搜尋您自動化帳戶中的記錄，請選取 [更新管理]，然後開啟與您的部署相關聯的 Log Analytics 工作區。

您也可以自訂記錄查詢，或從不同的用戶端使用這些查詢。 請參閱 [Log Analytics 搜尋 API 文件](https://dev.loganalytics.io/)。

## <a name="query-update-records"></a>查詢更新記錄

更新管理會收集 Windows 和 Linux VM 的記錄，以及記錄搜尋結果中顯示的資料類型。 下列幾節會說明這些記錄。

### <a name="query-required-updates"></a>查詢所需的更新

建立類型為 `RequiredUpdate` 的記錄，代表電腦所需的更新。 這些記錄具有下表中的屬性：

| 屬性 | 描述 | 
|----------|-------------|
| 電腦 | 報告機器的完整網域名稱。 |
| KBID | Windows Update 的知識庫文章識別碼。 |
| ManagementGroupName | Operations Manager 管理群組或 Log Analytics 工作區的名稱。 | 
| Products | 適用更新的產品。 | 
| PublishDate | 已準備好從 Windows Update 下載並安裝更新的日期。 |
| 伺服器 | | 
| SourceHealthServiceId | 代表 Log Analytics Windows 代理程式識別碼的唯一識別碼。 |
| SourceSystem | *OperationsManager* | 
| TenantId | 代表 Azure Active Directory 的組織執行個體唯一識別碼。 | 
| TimeGenerated | 記錄的建立日期和時間。 | 
| 類型 | *更新* | 
| UpdateClassification | 表示可套用的更新類型。 若為 Windows：<br> *重大更新*<br> *安全性更新*<br> *更新彙總套件*<br> *Feature Pack*<br> *Service Pack*<br> *定義更新*<br> *工具*<br> *更新*。 若為 Linux：<br> *重大更新和安全性更新*<br> *其他* |
| UpdateSeverity | 弱點的嚴重性評等。 值為：<br> *嚴重*<br> *重要*<br> *中*<br> *低* |
| UpdateTitle | 更新的標題。|

### <a name="query-update-record"></a>查詢更新記錄

系統會建立類型為 `Update` 的記錄，代表可用的更新以及電腦的安裝狀態。 這些記錄具有下表中的屬性：

| 屬性 | 描述 | 
|----------|-------------|
| ApprovalSource | 僅適用於 Windows 作業系統。 記錄的核准來源。 此值為 Microsoft Update。 |
| 已核准 | 如果已核准記錄，則為 True，否則為 False。 |
| 分類 | 核准分類。 此值為「更新」。 |
| 電腦 | 報告機器的完整網域名稱。 |
| ComputerEnvironment | 環境。 可能的值為 Azure 或非 Azure。 |
| MSRCBulletinID | 安全性公告識別碼。 | 
| MSRCSeverity | 弱點的嚴重性評等。 值為：<br> 重大<br> 重要事項<br> 中度<br> 低 |  
| KBID | Windows Update 的知識庫文章識別碼。 |
| ManagementGroupName | Operations Manager 管理群組或 Log Analytics 工作區的名稱。 |
| UpdateID | 軟體更新的唯一識別碼。 |
| RevisionNumber | 更新特定修訂的修訂編號。 |
| 選用 | 如果記錄為選擇性，則為 True，否則為 False。 | 
| RebootBehavior | 安裝/解除安裝更新之後的重新開機行為。 |
| _ResourceId | 記錄相關資源的唯一識別碼。 |
| 類型 | 記錄類型。 此值為「更新」。 |
| VMUUID | 虛擬機器的唯一識別碼。 |
| MG | 管理群組或 Log Analytics 工作區的唯一識別碼。 | 
| TenantId | 代表 Azure Active Directory 的組織執行個體唯一識別碼。 | 
| SourceSystem | 記錄的來源系統。 值為 `OperationsManager`。 | 
| TimeGenerated | 建立記錄的日期和時間。 | 
| SourceComputerId | 代表來源電腦的唯一識別碼。 | 
| Title | 更新的標題。 |
| PublishedDate (UTC) | 已準備好從 Windows Update 下載並安裝更新的日期。  |
| UpdateState | 更新的目前狀態。 | 
| Products | 適用更新的產品。 |
| SubscriptionId | Azure 訂用帳戶的唯一識別碼。 | 
| ResourceGroup | 資源所屬資源群組的名稱。 | 
| ResourceProvider | 資源提供者。 | 
| 資源 | 資源名稱。 | 
| ResourceType | 資源類型。 | 

### <a name="query-update-agent-record"></a>查詢更新代理程式記錄

系統會建立類型為 `UpdateAgent` 的記錄，以提供電腦上更新代理程式的詳細資料。 這些記錄具有下表中的屬性：

| 屬性 | 描述 | 
|----------|-------------|
| AgeofOldestMissingRequiredUpdate | | 
| AutomaticUpdateEnabled | | 
| 電腦 | 報告機器的完整網域名稱。 |
| DaySinceLastUpdateBucket | | 
| ManagementGroupName | Operations Manager 管理群組或 Log Analytics 工作區的名稱。 |
| OSVersion | 作業系統的版本。 |
| 伺服器 | |
| SourceHealthServiceId | 代表 Log Analytics Windows 代理程式識別碼的唯一識別碼。 |
| SourceSystem | 記錄的來源系統。 值為 `OperationsManager`。 | 
| TenantId | 代表 Azure Active Directory 的組織執行個體唯一識別碼。 |
| TimeGenerated | 建立記錄的日期和時間。 |
| 類型 | 記錄類型。 此值為「更新」。 | 
| WindowsUpdateAgentVersion | Windows Update 代理程式的版本。 |
| WSUSServer | 如果 Windows Update 代理程式有問題，就會發生錯誤，以協助進行疑難排解。 |

### <a name="query-update-deployment-status-record"></a>查詢更新部署狀態記錄

建立類型為 `UpdateRunProgress` 的記錄，依照電腦提供排程部署的更新部署狀態。 這些記錄具有下表中的屬性：

| 屬性 | 描述 | 
|----------|-------------|
| 電腦 | 報告機器的完整網域名稱。 |
| ComputerEnvironment | 環境。 值為 Azure 或非 Azure。 | 
| CorrelationId | 更新的 Runbook 作業執行唯一識別碼。 |
| EndTime | 同步處理程序結束的時間。 | 
| ErrorResult | 無法安裝更新時所產生的 Windows Update 錯誤碼。 | 
| InstallationStatus | 用戶端電腦上更新的可能安裝狀態，<br> `NotStarted` - 尚未觸發作業。<br> `FailedToStart` - 無法在電腦上啟動作業。<br> `Failed` - 作業已啟動但失敗，發生例外狀況。<br> `InProgress` - 進行中的作業。<br> `MaintenanceWindowExceeded` - 如果執行仍在進行中，但已達到維護時段間隔。<br> `Succeeded` - 作業成功。<br> `InstallFailed` - 無法成功安裝更新。<br> `NotIncluded`<br> `Excluded` |
| KBID | Windows Update 的知識庫文章識別碼。 | 
| ManagementGroupName | Operations Manager 管理群組或 Log Analytics 工作區的名稱。 |
| OSType | 作業系統的類型。 值為 Windows 或 Linux。 | 
| Products | 適用更新的產品。 |
| 資源 | 資源名稱。 | 
| ResourceId | 記錄相關資源的唯一識別碼。 |
| ResourceProvider | 資源提供者。 | 
| ResourceType | 資源類型。 | 
| SourceComputerId | 代表來源電腦的唯一識別碼。 | 
| SourceSystem | 記錄的來源系統。 值為 `OperationsManager`。 |
| StartTime | 排定安裝更新的時間。 |
| SubscriptionId | Azure 訂用帳戶的唯一識別碼。 | 
| SucceededOnRetry | 指出第一次嘗試時更新執行是否失敗的值，而目前的作業是重試嘗試。 |
| TimeGenerated | 建立記錄的日期和時間。 |
| Title | 更新的標題。 |
| 類型 | 更新的類型。 值為 `UpdateRunProgress`。 |
| UpdateId | 軟體更新的唯一識別碼。 |
| VMUUID | 虛擬機器的唯一識別碼。 |
| ResourceId | 記錄相關資源的唯一識別碼。 |

### <a name="query-update-summary-record"></a>查詢更新摘要記錄

建立類型為 `UpdateSummary` 的記錄，依照電腦提供更新摘要。 這些記錄具有下表中的屬性：

| 屬性 | 描述 | 
|----------|-------------|
| 電腦 | 報告機器的完整網域名稱。 |
| ComputerEnvironment | 環境。 值為 Azure 或非 Azure。 | 
| CriticalUpdatesMissing | 遺漏的可套用重大更新數目。 | 
| ManagementGroupName | Operations Manager 管理群組或 Log Analytics 工作區的名稱。 |
| NETRuntimeVersion | Windows 電腦上安裝的 .NET Framework 版本。 |
| OldestMissingSecurityUpdateBucket | 最舊遺漏安全性貯體的規範。 值為：<br> 如果值少於 30 天則為「最近」<br> 30 天前<br> 60 天前<br> 90 天前<br> 120 天前<br> 150 天前<br> 180 天前<br> 當值大於 180 天時則為「較舊」。 | 
| OldestMissingSecurityUpdateInDays | 最舊更新被偵測為可套用但尚未未安裝的總天數。 |
| OsVersion | 作業系統的版本。 |
| OtherUpdatesMissing | 遺漏偵測到的更新計數。 |
| 資源 | 記錄的資源名稱。 | 
| ResourceGroup | 包含資源的資源群組名稱。 |
| ResourceId | 記錄相關資源的唯一識別碼。 |
| ResourceProvider | 資源提供者。 |
| ResourceType | 資源類型。 |
| RestartPending | 如果重新開機暫止，則為 True，否則為 False。 |
| SecurityUpdatesMissing | 可套用的遺漏安全性更新計數。| 
| SourceComputerId | 虛擬機器的唯一識別碼。 |
| SourceSystem | 記錄的來源系統。 值為 `OpsManager`。 | 
| SubscriptionId | Azure 訂用帳戶的唯一識別碼。 |
| TimeGenerated | 建立記錄的日期和時間。 |
| TotalUpdatesMissing | 可套用的遺漏更新總數。 | 
| 類型 | 記錄類型。 值為 `UpdateSummary`。 |
| VMUUID | 虛擬機器的唯一識別碼。 |
| WindowsUpdateAgentVersion | Windows Update 代理程式的版本。 |
| WindowsUpdateSetting | Windows Update 代理程式的狀態。 可能的值包括：<br> `Scheduled installation`<br> `Notify before installation`<br> `Error returned from unhealthy WUA agent` | 
| WSUSServer | 如果 Windows Update 代理程式有問題，就會發生錯誤，以協助進行疑難排解。 |
| _ResourceId | 記錄相關資源的唯一識別碼。 |

## <a name="sample-queries"></a>範例查詢

以下各節針對「更新管理」收集的更新記錄，提供範例記錄查詢。

### <a name="confirm-that-non-azure-machines-are-enabled-for-update-management"></a>確認非 Azure 機器已啟用更新管理

若要確認直接連線的機器正與 Azure 監視器記錄通訊，請執行下列其中一個記錄搜尋。

#### <a name="linux"></a>Linux

```loganalytics
Heartbeat
| where OSType == "Linux" | summarize arg_max(TimeGenerated, *) by SourceComputerId | top 500000 by Computer asc | render table
```

#### <a name="windows"></a>Windows

```loganalytics
Heartbeat
| where OSType == "Windows" | summarize arg_max(TimeGenerated, *) by SourceComputerId | top 500000 by Computer asc | render table
```

在 Windows 電腦上，您可以檢閱下列資訊來確認與 Azure 監視器記錄的代理程式連線能力︰

1. 在 [控制台] 中，開啟 [Microsoft Monitoring Agent]。 在 [Azure Log Analytics] 索引標籤上，代理程式會顯示下列訊息：**Microsoft Monitoring Agent 已成功連線到 Log Analytics**。
2. 開啟 Windows 事件記錄檔。 移至 [應用程式及服務記錄\Operations Manager] 並從來源 [服務連接器] 搜尋事件識別碼 3000 和事件識別碼 5002。 這些事件表示電腦已向 Log Analytics 工作區註冊，並且正在接收設定。

如果代理程式無法與 Azure 監視器記錄通訊，而且代理程式已設定為透過防火牆或 Proxy 伺服器來與網際網路通訊，請確認防火牆或 Proxy 伺服器已正確設定。 若要了解如何確認防火牆或 proxy 伺服器已正確設定，請參閱 [Windows 代理程式的網路設定](../azure-monitor/platform/agent-windows.md)或 [Linux 代理程式的網路設定](../log-analytics/log-analytics-agent-linux.md)。

> [!NOTE]
> 如果您的 Linux 系統已設定為與 Proxy 或 Log Analytics 閘道進行通訊，而且您要啟用「更新管理」，請使用下列命令來更新 `proxy.conf` 權限，以將檔案讀取權限授予 omiuser 群組：
>
> `sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf`
> `sudo chmod 644 /etc/opt/microsoft/omsagent/proxy.conf`

在執行評估之後，新增的 Linux 代理程式狀態會顯示為「已更新」。 此程序可能需要多達 6 小時的時間。

若要確認 Operations Manager 管理群組正在與 Azure 監視器記錄通訊，請參閱[驗證 Operations Manager 與 Azure 監視器記錄的整合](../azure-monitor/platform/om-agents.md#validate-operations-manager-integration-with-azure-monitor)。

### <a name="single-azure-vm-assessment-queries-windows"></a>單一 Azure VM 評估查詢 (Windows)

將 VMUUID 值取代為您目前查詢之虛擬機器的 VM GUID。 您可以在 Azure 監視器記錄中執行以下查詢來尋找應該使用的 VMUUID：`Update | where Computer == "<machine name>" | summarize by Computer, VMUUID`

#### <a name="missing-updates-summary"></a>缺少的更新摘要

```loganalytics
Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and VMUUID=~"b08d5afa-1471-4b52-bd95-a44fea6e4ca8"
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| summarize by UpdateID, Classification
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security")
```

#### <a name="missing-updates-list"></a>缺少的更新清單

```loganalytics
Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and VMUUID=~"8bf1ccc6-b6d3-4a0b-a643-23f346dfdf82"
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Title, KBID, PublishedDate, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| project-away UpdateState, Approved, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), displayName=any(Title), publishedDate=min(PublishedDate), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(UpdateID, "_", KBID), classification=Classification, InformationId=strcat("KB", KBID), InformationUrl=iff(isnotempty(KBID), strcat("https://support.microsoft.com/kb/", KBID), ""), osType=2
| sort by ClassificationWeight desc, computersCount desc, displayName asc
| extend informationLink=(iff(isnotempty(InformationId) and isnotempty(InformationUrl), toobject(strcat('{ "uri": "', InformationUrl, '", "text": "', InformationId, '", "target": "blank" }')), toobject('')))
| project-away ClassificationWeight, InformationId, InformationUrl
```

### <a name="single-azure-vm-assessment-queries-linux"></a>單一 Azure VM 評估查詢 (Linux)

針對某些 Linux 發行版本，[位元組順序](https://en.wikipedia.org/wiki/Endianness)與 Azure Resource Manager 的 VMUUID 值以及 Azure 監視器記錄中儲存的內容不相符。 以下查詢會檢查這上述兩者的位元組順序是否相符。 將 VMUUID 值取代為 GUID 的位元組由大到小和位元組由小到大格式，以正確地傳回結果。 您可以在 Azure 監視器記錄中執行以下查詢來尋找應該使用的 VMUUID：`Update | where Computer == "<machine name>"
| summarize by Computer, VMUUID`

#### <a name="missing-updates-summary"></a>缺少的更新摘要

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and (VMUUID=~"625686a0-6d08-4810-aae9-a089e68d4911" or VMUUID=~"a0865662-086d-1048-aae9-a089e68d4911")
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| summarize by Product, ProductArch, Classification
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security")
```

#### <a name="missing-updates-list"></a>缺少的更新清單

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and (VMUUID=~"625686a0-6d08-4810-aae9-a089e68d4911" or VMUUID=~"a0865662-086d-1048-aae9-a089e68d4911")
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, BulletinUrl, BulletinID) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| project-away UpdateState, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(Product, "_", ProductArch), displayName=Product, productArch=ProductArch, classification=Classification, InformationId=BulletinID, InformationUrl=tostring(split(BulletinUrl, ";", 0)[0]), osType=1
| sort by ClassificationWeight desc, computersCount desc, displayName asc
| extend informationLink=(iff(isnotempty(InformationId) and isnotempty(InformationUrl), toobject(strcat('{ "uri": "', InformationUrl, '", "text": "', InformationId, '", "target": "blank" }')), toobject('')))
| project-away ClassificationWeight, InformationId, InformationUrl

```

### <a name="multi-vm-assessment-queries"></a>多部 VM 評估查詢

#### <a name="computers-summary"></a>電腦摘要

```loganalytics
Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(14h) and OSType!="Linux"
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Approved, Optional, Classification) by SourceComputerId, UpdateID
    | distinct SourceComputerId, Classification, UpdateState, Approved, Optional
    | summarize WorstMissingUpdateSeverity=max(iff(UpdateState=~"Needed" and (Optional==false or Classification has "Critical" or Classification has "Security") and Approved!=false, iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1)), 0)) by SourceComputerId
)
on SourceComputerId
| extend WorstMissingUpdateSeverity=coalesce(WorstMissingUpdateSeverity, -1)
| summarize computersBySeverity=count() by WorstMissingUpdateSeverity
| union (Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(5h) and OSType=="Linux"
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by SourceComputerId, Product, ProductArch
    | distinct SourceComputerId, Classification, UpdateState
    | summarize WorstMissingUpdateSeverity=max(iff(UpdateState=~"Needed", iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1)), 0)) by SourceComputerId
)
on SourceComputerId
| extend WorstMissingUpdateSeverity=coalesce(WorstMissingUpdateSeverity, -1)
| summarize computersBySeverity=count() by WorstMissingUpdateSeverity)
| summarize assessedComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity>-1), notAssessedComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==-1), computersNeedCriticalUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==4), computersNeedSecurityUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==2), computersNeedOtherUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==1), upToDateComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==0)
| summarize assessedComputersCount=sum(assessedComputersCount), computersNeedCriticalUpdatesCount=sum(computersNeedCriticalUpdatesCount),  computersNeedSecurityUpdatesCount=sum(computersNeedSecurityUpdatesCount), computersNeedOtherUpdatesCount=sum(computersNeedOtherUpdatesCount), upToDateComputersCount=sum(upToDateComputersCount), notAssessedComputersCount=sum(notAssessedComputersCount)
| extend allComputersCount=assessedComputersCount+notAssessedComputersCount
```

#### <a name="missing-updates-summary"></a>缺少的更新摘要

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| summarize by Product, ProductArch, Classification
| union (Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| summarize by UpdateID, Classification )
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security")
```

#### <a name="computers-list"></a>電腦清單

```loganalytics
Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions, Computer, ResourceId, ComputerEnvironment, VMUUID) by SourceComputerId
| where Solutions has "updates"
| extend vmuuId=VMUUID, azureResourceId=ResourceId, osType=1, environment=iff(ComputerEnvironment=~"Azure", 1, 2), scopedToUpdatesSolution=true, lastUpdateAgentSeenTime=""
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(5h) and OSType=="Linux" and SourceComputerId in ((Heartbeat
    | where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
    | summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
    | where Solutions has "updates"
    | distinct SourceComputerId))
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Product, Computer, ComputerEnvironment) by SourceComputerId, Product, ProductArch
    | summarize Computer=any(Computer), ComputerEnvironment=any(ComputerEnvironment), missingCriticalUpdatesCount=countif(Classification has "Critical" and UpdateState=~"Needed"), missingSecurityUpdatesCount=countif(Classification has "Security" and UpdateState=~"Needed"), missingOtherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security" and UpdateState=~"Needed"), lastAssessedTime=max(TimeGenerated), lastUpdateAgentSeenTime="" by SourceComputerId
    | extend compliance=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0, 2, 1)
    | extend ComplianceOrder=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0 or missingOtherUpdatesCount > 0, 1, 3)
)
on SourceComputerId
| project id=SourceComputerId, displayName=Computer, sourceComputerId=SourceComputerId, scopedToUpdatesSolution=true, missingCriticalUpdatesCount=coalesce(missingCriticalUpdatesCount, -1), missingSecurityUpdatesCount=coalesce(missingSecurityUpdatesCount, -1), missingOtherUpdatesCount=coalesce(missingOtherUpdatesCount, -1), compliance=coalesce(compliance, 4), lastAssessedTime, lastUpdateAgentSeenTime, osType=1, environment=iff(ComputerEnvironment=~"Azure", 1, 2), ComplianceOrder=coalesce(ComplianceOrder, 2)
| union(Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions, Computer, ResourceId, ComputerEnvironment, VMUUID) by SourceComputerId
| where Solutions has "updates"
| extend vmuuId=VMUUID, azureResourceId=ResourceId, osType=2, environment=iff(ComputerEnvironment=~"Azure", 1, 2), scopedToUpdatesSolution=true, lastUpdateAgentSeenTime=""
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(14h) and OSType!="Linux" and SourceComputerId in ((Heartbeat
    | where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
    | summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
    | where Solutions has "updates"
    | distinct SourceComputerId))
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Title, Optional, Approved, Computer, ComputerEnvironment) by Computer, SourceComputerId, UpdateID
    | summarize Computer=any(Computer), ComputerEnvironment=any(ComputerEnvironment), missingCriticalUpdatesCount=countif(Classification has "Critical" and UpdateState=~"Needed" and Approved!=false), missingSecurityUpdatesCount=countif(Classification has "Security" and UpdateState=~"Needed" and Approved!=false), missingOtherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security" and UpdateState=~"Needed" and Optional==false and Approved!=false), lastAssessedTime=max(TimeGenerated), lastUpdateAgentSeenTime="" by SourceComputerId
    | extend compliance=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0, 2, 1)
    | extend ComplianceOrder=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0 or missingOtherUpdatesCount > 0, 1, 3)
)
on SourceComputerId
| project id=SourceComputerId, displayName=Computer, sourceComputerId=SourceComputerId, scopedToUpdatesSolution=true, missingCriticalUpdatesCount=coalesce(missingCriticalUpdatesCount, -1), missingSecurityUpdatesCount=coalesce(missingSecurityUpdatesCount, -1), missingOtherUpdatesCount=coalesce(missingOtherUpdatesCount, -1), compliance=coalesce(compliance, 4), lastAssessedTime, lastUpdateAgentSeenTime, osType=2, environment=iff(ComputerEnvironment=~"Azure", 1, 2), ComplianceOrder=coalesce(ComplianceOrder, 2) )
| order by ComplianceOrder asc, missingCriticalUpdatesCount desc, missingSecurityUpdatesCount desc, missingOtherUpdatesCount desc, displayName asc
| project-away ComplianceOrder
```

#### <a name="missing-updates-list"></a>缺少的更新清單

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, BulletinUrl, BulletinID) by SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| project-away UpdateState, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(Product, "_", ProductArch), displayName=Product, productArch=ProductArch, classification=Classification, InformationId=BulletinID, InformationUrl=tostring(split(BulletinUrl, ";", 0)[0]), osType=1
| union(Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Title, KBID, PublishedDate, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| project-away UpdateState, Approved, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), displayName=any(Title), publishedDate=min(PublishedDate), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(UpdateID, "_", KBID), classification=Classification, InformationId=strcat("KB", KBID), InformationUrl=iff(isnotempty(KBID), strcat("https://support.microsoft.com/kb/", KBID), ""), osType=2)
| sort by ClassificationWeight desc, computersCount desc, displayName asc
| extend informationLink=(iff(isnotempty(InformationId) and isnotempty(InformationUrl), toobject(strcat('{ "uri": "', InformationUrl, '", "text": "', InformationId, '", "target": "blank" }')), toobject('')))
| project-away ClassificationWeight, InformationId, InformationUrl
```

## <a name="next-steps"></a>後續步驟

* 如需 Azure 監視器記錄的詳細資訊，請參閱 [Azure 監視器記錄](../log-analytics/log-analytics-log-searches.md)。
* 如需警示的說明，請參閱[設定警示](automation-tutorial-update-management.md#configure-alerts)。
