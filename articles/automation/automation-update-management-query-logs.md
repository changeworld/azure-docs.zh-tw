---
title: 查詢 Azure 更新管理日誌
description: 本文介紹如何在日誌分析工作區中查詢日誌以進行更新管理。
services: automation
ms.subservice: update-management
ms.date: 03/11/2020
ms.topic: conceptual
ms.openlocfilehash: f31168d47f31d8e740c95cb3d9e449f473cc78dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79216836"
---
# <a name="query-update-records-for-update-management-in-azure-monitor-logs"></a>在 Azure 監視器日誌中查詢更新管理記錄

除了更新管理解決方案中提供的詳細資訊外，您還可以針對日誌分析工作區中存儲的日誌進行搜索。 在解決方案頁中，在左側窗格中選擇 **"日誌**"。 將打開 **"日誌搜索"** 頁。

您還可以通過訪問：[日誌分析搜索 API 文檔](https://dev.loganalytics.io/)，瞭解如何自訂查詢或從不同用戶端使用它們。

## <a name="update-records"></a>更新記錄

由 Windows 和 Linux VM 的更新管理以及日誌搜尋結果中顯示的資料類型收集的記錄。 以下各節介紹這些記錄。

### <a name="required-updates"></a>必要的更新

創建具有 類型的`RequiredUpdate`記錄，表示電腦所需的更新。 這些記錄具有下表中的屬性：

| 屬性 | 描述 | 
|----------|-------------|
| 電腦 | 報告機的完全限定功能變數名稱。 |
| KBID | Windows 更新的知識庫文章 ID。 |
| ManagementGroupName | 操作管理器管理組或日誌分析工作區的名稱。 | 
| Products | 更新適用于的產品。 | 
| PublishDate | 更新準備好從 Windows 更新下載和安裝的日期。 |
| 伺服器 | | 
| SourceHealthServiceId | 表示日誌分析 Windows 代理 ID 的唯一識別碼。 |
| SourceSystem | *OperationsManager* | 
| TenantId | 表示 Azure 活動目錄的組織實例的唯一識別碼。 | 
| TimeGenerated | 記錄的建立日期和時間。 | 
| 類型 | *更新* | 
| UpdateClassification | 指示可應用的更新類型。 若為 Windows：<br> *重大更新*<br> *安全更新*<br> *更新彙總套件*<br> *Feature Pack*<br> *服務包*<br> *定義更新*<br> *工具*<br> *更新*. 若為 Linux：<br> *重大更新和安全性更新*<br> *其他* |
| 更新嚴重性 | 漏洞的嚴重性評級。 值為：<br> *關鍵*<br> *重要*<br> *中度*<br> *低* |
| UpdateTitle | 更新的標題。|

### <a name="update"></a>更新

`Update`將創建一個記錄，該記錄表示電腦可用的更新及其安裝狀態。 這些記錄具有下表中的屬性：

| 屬性 | 描述 | 
|----------|-------------|
| 批准來源 | 僅適用于 Windows 作業系統。 價值是*微軟更新*。 |
| 已核准 | *真**假* |
| 分類 | *更新* |
| 電腦 | 報告機的完全限定功能變數名稱。 |
| 電腦環境 | *Azure* *或非 Azure*。 |
| MSRCBulletinID | 安全公告 ID 號 | 
| MSRCSeverity | 漏洞的嚴重性評級。 值為：<br> *關鍵*<br> *重要*<br> *中度*<br> *低* |  
| KBID | Windows 更新的知識庫文章 ID。 |
| ManagementGroupName | 操作管理器管理組或日誌分析工作區的名稱。 |
| UpdateID | 軟體更新的唯一識別碼。 |
| RevisionNumber | 更新的特定修訂的修訂號。 |
| 選用 | *真**假* | 
| RebootBehavior | 安裝/卸載更新後的重新開機行為。 |
| _ResourceId | 記錄關聯的資源的唯一識別碼。 |
| 類型 | *更新* |
| VMUUID | 虛擬機器的唯一識別碼。 |
| MG | 管理組或日誌分析工作區的唯一識別碼。 | 
| TenantId | 表示 Azure 活動目錄的組織實例的唯一識別碼。 | 
| SourceSystem | *OperationsManager* | 
| TimeGenerated | 記錄的建立日期和時間。 | 
| SourceComputerId | 表示源電腦的唯一識別碼。 | 
| Title | 更新的標題。 |
| 發佈日期 （UTC） | 更新準備好從 Windows 更新下載和安裝的日期。  |
| UpdateState | 更新的目前狀態。 | 
| Products | 更新適用于的產品。 |
| SubscriptionId | Azure 訂用帳戶的唯一識別碼。 | 
| ResourceGroup | 資源的成員的資源組的名稱。 | 
| ResourceProvider | 指定資來源提供者。 | 
| 資源 | 資源名稱。 | 
| ResourceType | 資源類型名稱。 | 

### <a name="update-agent"></a>更新代理程式

將創建具有 類型的`UpdateAgent`記錄，該記錄提供電腦上的更新代理的詳細資訊。 這些記錄具有下表中的屬性：

| 屬性 | 描述 | 
|----------|-------------|
| 最舊缺失的一年年齡需要更新 | | 
| AutomaticUpdateEnabled | | 
| 電腦 | 報告機的完全限定功能變數名稱。 |
| 日自上次更新桶 | | 
| ManagementGroupName | 操作管理器管理組或日誌分析工作區的名稱。 |
| OSVersion | 作業系統的版本。 |
| 伺服器 | |
| SourceHealthServiceId | 表示日誌分析 Windows 代理 ID 的唯一識別碼。 |
| SourceSystem | *OperationsManager* | 
| TenantId | 表示 Azure 活動目錄的組織實例的唯一識別碼。 |
| TimeGenerated | 記錄的建立日期和時間。 |
| 類型 | *更新* | 
| WindowsUpdateAgentVersion | Windows 更新代理的版本。 |
| WSUSServer | 如果 Windows 更新代理出現問題以説明進行故障排除，請顯示錯誤。 |

### <a name="update-deployment-status"></a>更新部署狀態 

將創建具有 類型的`UpdateRunProgress`記錄，該記錄按電腦提供計畫部署的更新部署狀態。 這些記錄具有下表中的屬性：

| 屬性 | 描述 | 
|----------|-------------|
| 電腦 | 報告機的完全限定功能變數名稱。 |
| 電腦環境 | *Azure* *或非 Azure*。 | 
| CorrelationId | 運行更新的 Runbook 作業的唯一識別碼。 |
| EndTime | 同步過程結束的時間。 | 
| 錯誤結果 | 如果更新安裝失敗，則生成 Windows 更新錯誤代碼。 | 
| 安裝狀態 | 用戶端電腦上的更新的可能安裝狀態"*正在進行*，*成功*"*部分失敗*。 |
| KBID | Windows 更新的知識庫文章 ID。 | 
| ManagementGroupName | 操作管理器管理組或日誌分析工作區的名稱。 |
| OSType | 指定作業系統 *、Windows*或*Linux*的類型。 | 
| Products | 更新適用于的產品。 |
| 資源 | 資源名稱。 | 
| ResourceId | 記錄關聯的資源的唯一識別碼。 |
| ResourceProvider | 指定資來源提供者。 | 
| ResourceType | 資源類型名稱。 | 
| SourceComputerId | 表示源電腦的唯一識別碼。 | 
| SourceSystem | *OperationsManager* |
| StartTime | 計畫安裝更新的時間。 |
| SubscriptionId | Azure 訂用帳戶的唯一識別碼。 | 
| 成功重新測量 | 顯示第一次嘗試的更新執行失敗以及當前操作是重試嘗試時。 |
| TimeGenerated | 記錄的建立日期和時間。 |
| Title | 更新的標題。 |
| 類型 | *UpdateRunProgress* |
| 更新 Id | 軟體更新的唯一識別碼。 |
| VMUUID | 虛擬機器的唯一識別碼。 |
| _ResourceId | 記錄關聯的資源的唯一識別碼。 |

### <a name="update-summary"></a>更新摘要 

將創建具有 類型的`UpdateSummary`記錄，該記錄按電腦提供更新摘要。 這些記錄具有下表中的屬性：

| 屬性 | 描述 | 
|----------|-------------|
| 電腦 | 報告機的完全限定功能變數名稱。 |
| 電腦環境 | *Azure* *或非 Azure*。 | 
| CriticalUpdatesMissing | 缺少的重大更新數。 | 
| ManagementGroupName | 操作管理器管理組或日誌分析工作區的名稱。 |
| NETRuntimeVersion | 在 Windows 電腦上安裝的 .NET 框架的版本。 |
| OldestMissingSecurityUpdateBucket | 值為：<br> *最近*<br> *30 天前*<br> *60 天前*<br> *較舊* | 
| OldestMissingSecurityUpdateInDays | 檢測到的未安裝的最早更新的總天數。 |
| OsVersion | 作業系統的版本。 |
| OtherUpdatesMissing | 缺少檢測到的更新計數。 |
| 資源 |  資源名稱。 | 
| ResourceGroup | 資源的成員的資源組的名稱。 |
| ResourceId | 記錄關聯的資源的唯一識別碼。 |
| ResourceProvider | 指定資來源提供者。 |
| ResourceType | 資源類型名稱。 |
| 重新開機掛起 | *真**或假*。 |
| SecurityUpdatesMissing | 適用的安全更新缺失計數。| 
| SourceComputerId | 虛擬機器的唯一識別碼。 |
| SourceSystem | *OpsManager* | 
| SubscriptionId | Azure 訂用帳戶的唯一識別碼。 |
| TimeGenerated | 記錄的建立日期和時間。 |
| TotalUpdatesMissing | 適用的缺少更新的總數。 | 
| 類型 | *UpdateSummary* |
| VMUUID | 虛擬機器的唯一識別碼。 |
| WindowsUpdateAgentVersion | Windows 更新代理的版本。 |
| WindowsUpdateSetting | 顯示 Windows 更新代理的狀態。 可能的值包括：<br> *計畫安裝*<br> *安裝前通知*<br> 從不正常的 WUA 代理返回的錯誤。 | 
| WSUSServer | 如果 Windows 更新代理出現問題以説明進行故障排除，請顯示錯誤。 |
| _ResourceId | 記錄關聯的資源的唯一識別碼。 |

## <a name="sample-queries"></a>範例查詢

以下各節為為更新管理收集的更新記錄提供示例日誌查詢。

### <a name="confirm-that-non-azure-machines-are-onboarded"></a>確認非 Azure 機器已上線

要確認直接連接的電腦正在與 Azure 監視器日誌通信，請運行以下日誌搜索之一。

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

在 Windows 電腦上，您可以查看以下資訊以驗證代理連接 Azure 監視器日誌：

1. 在 [控制台] 中，開啟 [Microsoft Monitoring Agent]****。 在 [Azure Log Analytics]**** 索引標籤中，代理程式會顯示以下訊息：**Microsoft Monitoring Agent 已成功連線至 Log Analytics**。
2. 開啟 Windows 事件記錄檔。 移至 [應用程式及服務記錄\Operations Manager]**** 並從來源 [服務連接器]**** 搜尋事件識別碼 3000 和事件識別碼 5002。 這些事件表示電腦已向 Log Analytics 工作區註冊，並且正在接收設定。

如果代理無法與 Azure 監視器日誌通信，並且代理配置為通過防火牆或代理伺服器與 Internet 通信，請確認防火牆或代理伺服器配置正確。 若要了解如何確認防火牆或 proxy 伺服器已正確設定，請參閱 [Windows 代理程式的網路設定](../azure-monitor/platform/agent-windows.md)或 [Linux 代理程式的網路設定](../log-analytics/log-analytics-agent-linux.md)。

> [!NOTE]
> 如果您的 Linux 系統已設定為與 Proxy 或「Log Analytics 閘道」進行通訊，而且您要將此解決方案上線，則請更新 *proxy.conf* 權限，以使用下列命令將檔案讀取權限授與 omiuser 群組：
>
> `sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf`
> `sudo chmod 644 /etc/opt/microsoft/omsagent/proxy.conf`

在執行評估之後，新增的 Linux 代理程式狀態會顯示為「已更新」****。 此程序可能需要多達 6 小時的時間。

要確認操作管理器管理組正在與 Azure 監視器日誌通信，請參閱[驗證操作管理器與 Azure 監視器日誌的集成](../azure-monitor/platform/om-agents.md#validate-operations-manager-integration-with-azure-monitor)。

### <a name="single-azure-vm-assessment-queries-windows"></a>單一 Azure VM 評估查詢 (Windows)

將 VMUUID 值取代為您目前查詢之虛擬機器的 VM GUID。 您可以在 Azure 監視器日誌中運行以下查詢來查找應使用的 VMUUID：`Update | where Computer == "<machine name>" | summarize by Computer, VMUUID`

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

對於某些 Linux 發行版本，與來自 Azure 資源管理器的 VMUUID 值和存儲在 Azure 監視器日誌中的內容存在[內質](https://en.wikipedia.org/wiki/Endianness)不匹配。 以下查詢會檢查這上述兩者的位元組順序是否相符。 將 VMUUID 值取代為 GUID 的位元組由大到小和位元組由小到大格式，以正確地傳回結果。 您可以在 Azure 監視器日誌中運行以下查詢來查找應使用的 VMUUID：`Update | where Computer == "<machine name>"
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

* 使用 Azure[監視器日誌](../log-analytics/log-analytics-log-searches.md)中的日誌搜索查看詳細的更新資料。
* 針對更新部署狀態[建立警示](automation-tutorial-update-management.md#configure-alerts)。
