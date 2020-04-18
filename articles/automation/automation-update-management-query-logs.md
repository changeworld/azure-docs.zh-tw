---
title: 查詢 Azure 更新管理紀錄
description: 本文介紹如何在日誌分析工作區中查詢日誌以進行更新管理。
services: automation
ms.subservice: update-management
ms.date: 04/06/2020
ms.topic: conceptual
ms.openlocfilehash: 09eacb42eff6ecf3a3fca2d7fb401f52195f5f2d
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617428"
---
# <a name="query-update-records-for-update-management-in-azure-monitor-logs"></a>在 Azure 監視器紀錄中查詢更新管理記錄

除了更新管理解決方案中提供的詳細資訊外,您還可以針對日誌分析工作區中存儲的日誌進行搜索。 在解決方案頁中,在左側窗格中,選擇 **「日誌**」。 將打開「日誌搜索」 頁。

您還可以瞭解如何自定義查詢或從不同的用戶端使用它們。 請參考[資料資訊分析搜尋 API 文件](https://dev.loganalytics.io/)。

## <a name="update-records"></a>更新記錄

更新管理收集 Windows 和 Linux VM 的記錄以及日誌搜尋結果中顯示的數據類型。 以下各節介紹這些記錄。

### <a name="required-updates"></a>必要的更新

創建具有 類型`RequiredUpdate`的 記錄,表示電腦所需的更新。 這些記錄具有下表中的屬性：

| 屬性 | 描述 | 
|----------|-------------|
| 電腦 | 報告機的完全限定功能變數名稱。 |
| KBID | Windows 更新的知識庫文章 ID。 |
| ManagementGroupName | 操作管理員管理組或日誌分析工作區的名稱。 | 
| Products | 更新適用於的產品。 | 
| PublishDate | 更新準備好從 Windows 更新下載和安裝的日期。 |
| 伺服器 | | 
| SourceHealthServiceId | 表示日誌分析 Windows 代理 ID 的唯一識別碼。 |
| SourceSystem | *OperationsManager* | 
| TenantId | 表示 Azure 活動目錄的組織實例的唯一標識碼。 | 
| TimeGenerated | 記錄的建立日期和時間。 | 
| 類型 | *更新* | 
| UpdateClassification | 指示可應用的更新類型。 若為 Windows：<br> *重大更新*<br> *安全性更新*<br> *更新彙總套件*<br> *Feature Pack*<br> *Service Pack*<br> *定義更新*<br> *工具*<br> *更新*. 若為 Linux：<br> *重大更新和安全性更新*<br> *其他* |
| 更新嚴重性 | 漏洞的嚴重性評級。 值為：<br> *關鍵*<br> *重要*<br> *中度*<br> *低* |
| UpdateTitle | 更新的標題。|

### <a name="update"></a>更新

`Update`將創建一個記錄,該記錄表示計算機可用的更新及其安裝狀態。 這些記錄具有下表中的屬性：

| 屬性 | 描述 | 
|----------|-------------|
| 批准來源 | 僅適用於 Windows 作業系統。 記錄的批准來源。 該值是微軟更新。 |
| 已核准 | 如果記錄已批准,則為 True,否則為 False。 |
| 分類 | 批准分類。 該值為"更新"。 |
| 電腦 | 報告機的完全限定功能變數名稱。 |
| 電腦環境 | 環境。 可能的值是 Azure 或非 Azure。 |
| MSRCBulletinID | 安全公告ID號。 | 
| MSRCSeverity | 漏洞的嚴重性評級。 值為：<br> 重大<br> 重要事項<br> 中度<br> 低 |  
| KBID | Windows 更新的知識庫文章 ID。 |
| ManagementGroupName | 操作管理員管理組或日誌分析工作區的名稱。 |
| UpdateID | 軟體更新的唯一標識碼。 |
| RevisionNumber | 更新的特定修訂的修訂號。 |
| 選用 | 如果記錄是可選的,則為 True,否則為 False。 | 
| RebootBehavior | 安裝/卸載更新后的重新啟動行為。 |
| _ResourceId | 與記錄關聯的資源的唯一標識符。 |
| 類型 | 記錄類型。 該值為"更新"。 |
| VMUUID | 虛擬機的唯一標識碼。 |
| MG | 管理組或日誌分析工作區的唯一標識碼。 | 
| TenantId | 表示組織 Azure 活動目錄實例的唯一標識碼。 | 
| SourceSystem | 記錄的源系統。 值為 `OperationsManager`。 | 
| TimeGenerated | 創建記錄的日期和時間。 | 
| SourceComputerId | 表示源電腦的唯一標識符。 | 
| Title | 更新的標題。 |
| 發佈日期 (UTC) | 更新準備好從 Windows 更新下載和安裝的日期。  |
| UpdateState | 更新的當前狀態。 | 
| Products | 更新適用的產品。 |
| SubscriptionId | Azure 訂用帳戶的唯一識別碼。 | 
| ResourceGroup | 資源所屬的資源組的名稱。 | 
| ResourceProvider | 資源提供者。 | 
| 資源 | 資源名稱。 | 
| ResourceType | 資源類型。 | 

### <a name="update-agent"></a>更新代理程式

將創建具有類型的`UpdateAgent`記錄,該記錄提供計算機上的更新代理的詳細資訊。 這些記錄具有下表中的屬性：

| 屬性 | 描述 | 
|----------|-------------|
| 最舊缺失的一年年齡需要更新 | | 
| AutomaticUpdateEnabled | | 
| 電腦 | 報告機的完全限定功能變數名稱。 |
| 日自上次更新桶 | | 
| ManagementGroupName | 操作管理員管理組或日誌分析工作區的名稱。 |
| OSVersion | 操作系統的版本。 |
| 伺服器 | |
| SourceHealthServiceId | 表示日誌分析 Windows 代理 ID 的唯一識別碼。 |
| SourceSystem | 記錄的源系統。 值為 `OperationsManager`。 | 
| TenantId | 表示組織 Azure 活動目錄實例的唯一標識碼。 |
| TimeGenerated | 創建記錄的日期和時間。 |
| 類型 | 記錄類型。 該值為"更新"。 | 
| WindowsUpdateAgentVersion | Windows 更新代理的版本。 |
| WSUSServer | 如果 Windows 更新代理出現問題,則出錯,以幫助進行故障排除。 |

### <a name="update-deployment-status"></a>更新部署狀態 

將創建具有類型的`UpdateRunProgress`記錄,該記錄按計算機提供計畫部署的更新部署狀態。 這些記錄具有下表中的屬性：

| 屬性 | 描述 | 
|----------|-------------|
| 電腦 | 報告機的完全限定功能變數名稱。 |
| 電腦環境 | 環境。 值為 Azure 或非 Azure。 | 
| CorrelationId | 運行更新的 Runbook 作業的唯一標識符。 |
| EndTime | 同步過程結束的時間。 | 
| 錯誤結果 | 如果更新安裝失敗,則生成 Windows 更新錯誤代碼。 | 
| 安裝狀態 | 用戶端計算機上的更新的可能安裝狀態,<br> `NotStarted`- 作業尚未觸發。<br> `FailedToStart`- 無法在機器上啟動作業。<br> `Failed`- 作業已啟動,但異常失敗。<br> `InProgress`- 工作正在進行中。<br> `MaintenanceWindowExceeded`- 如果執行仍然存在,但達到維護視窗間隔。<br> `Succeeded`-工作成功了<br> `InstallFailed`- 更新成功安裝失敗。<br> `NotIncluded`<br> `Excluded` |
| KBID | Windows 更新的知識庫文章 ID。 | 
| ManagementGroupName | 操作管理員管理組或日誌分析工作區的名稱。 |
| OSType | 作業系統的類型。 值是 Windows 或 Linux。 | 
| Products | 更新適用的產品。 |
| 資源 | 資源名稱。 | 
| ResourceId | 與記錄關聯的資源的唯一標識符。 |
| ResourceProvider | 資源提供者。 | 
| ResourceType | 資源類型。 | 
| SourceComputerId | 表示源電腦的唯一標識符。 | 
| SourceSystem | 記錄的源系統。 值為 `OperationsManager`。 |
| StartTime | 計劃安裝更新的時間。 |
| SubscriptionId | Azure 訂用帳戶的唯一識別碼。 | 
| 成功重新測量 | 指示第一次嘗試的更新執行失敗,當前操作是否為重試嘗試的值。 |
| TimeGenerated | 創建記錄的日期和時間。 |
| Title | 更新的標題。 |
| 類型 | 更新的類型。 值為 `UpdateRunProgress`。 |
| 更新 Id | 軟體更新的唯一標識碼。 |
| VMUUID | 虛擬機的唯一標識碼。 |
| ResourceId | 與記錄關聯的資源的唯一標識符。 |

### <a name="update-summary"></a>更新摘要 

將創建具有類型的`UpdateSummary`記錄,該記錄按計算機提供更新摘要。 這些記錄具有下表中的屬性：

| 屬性 | 描述 | 
|----------|-------------|
| 電腦 | 報告機的完全限定功能變數名稱。 |
| 電腦環境 | 環境。 值為 Azure 或非 Azure。 | 
| CriticalUpdatesMissing | 缺少的適用關鍵更新數。 | 
| ManagementGroupName | 操作管理員管理組或日誌分析工作區的名稱。 |
| NETRuntimeVersion | 在 Windows 電腦上安裝的 .NET 框架的版本。 |
| OldestMissingSecurityUpdateBucket | 最舊缺少的安全存儲桶的指定器。 值為：<br> 最近比值小於 30 天<br> 30 天前<br> 60 天前<br> 90 天前<br> 120 天前<br> 150 天前<br> 180 天前<br> 當值大於 180 天時,舊。 | 
| OldestMissingSecurityUpdateInDays | 檢測到的未安裝的最早更新的總天數。 |
| OsVersion | 操作系統的版本。 |
| OtherUpdatesMissing | 缺少檢測到的更新計數。 |
| 資源 | 記錄的資源的名稱。 | 
| ResourceGroup | 包含資源的資源組的名稱。 |
| ResourceId | 與記錄關聯的資源的唯一標識符。 |
| ResourceProvider | 資源提供者。 |
| ResourceType | 資源類型。 |
| 重新啟動暫停 | 如果重新啟動處於掛起狀態,則為 True,否則為 False。 |
| SecurityUpdatesMissing | 缺少的安全更新的計數。| 
| SourceComputerId | 虛擬機的唯一標識碼。 |
| SourceSystem | 記錄的源系統。 值為 `OpsManager`。 | 
| SubscriptionId | Azure 訂用帳戶的唯一識別碼。 |
| TimeGenerated | 創建記錄的日期和時間。 |
| TotalUpdatesMissing | 適用的缺少更新的總數。 | 
| 類型 | 記錄類型。 值為 `UpdateSummary`。 |
| VMUUID | 虛擬機的唯一標識碼。 |
| WindowsUpdateAgentVersion | Windows 更新代理的版本。 |
| WindowsUpdateSetting | Windows 更新代理的狀態。 可能的值包括：<br> `Scheduled installation`<br> `Notify before installation`<br> `Error returned from unhealthy WUA agent` | 
| WSUSServer | 如果 Windows 更新代理出現問題,則出錯,以幫助進行故障排除。 |
| _ResourceId | 與記錄關聯的資源的唯一標識符。 |

## <a name="sample-queries"></a>範例查詢

以下各節為為更新管理收集的更新記錄提供示例日誌查詢。

### <a name="confirm-that-non-azure-machines-are-onboarded"></a>確認非 Azure 機器已上線

要確認直接連接的電腦正在與 Azure 監視器日誌通信,請運行以下日誌搜索之一。

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

在 Windows 電腦上,您可以檢視以下資訊以驗證代理連線 Azure 監視器紀錄:

1. 在 [控制台] 中，開啟 [Microsoft Monitoring Agent]****。 在 [Azure Log Analytics]**** 索引標籤中，代理程式會顯示以下訊息：**Microsoft Monitoring Agent 已成功連線至 Log Analytics**。
2. 開啟 Windows 事件記錄檔。 移至 [應用程式及服務記錄\Operations Manager]**** 並從來源 [服務連接器]**** 搜尋事件識別碼 3000 和事件識別碼 5002。 這些事件表示電腦已向 Log Analytics 工作區註冊，並且正在接收設定。

如果代理無法與 Azure 監視器日誌通訊,並且代理配置為透過防火牆或代理伺服器與 Internet 通訊,請確認防火牆或代理伺服器配置正確。 若要了解如何確認防火牆或 proxy 伺服器已正確設定，請參閱 [Windows 代理程式的網路設定](../azure-monitor/platform/agent-windows.md)或 [Linux 代理程式的網路設定](../log-analytics/log-analytics-agent-linux.md)。

> [!NOTE]
> 如果您的 Linux 系統已設定為與 Proxy 或「Log Analytics 閘道」進行通訊，而且您要將此解決方案上線，則請更新 *proxy.conf* 權限，以使用下列命令將檔案讀取權限授與 omiuser 群組：
>
> `sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf`
> `sudo chmod 644 /etc/opt/microsoft/omsagent/proxy.conf`

在執行評估之後，新增的 Linux 代理程式狀態會顯示為「已更新」****。 此程序可能需要多達 6 小時的時間。

要確認操作管理員管理群組正在與 Azure 監視器紀錄通訊,請參考[授權操作管理員與 Azure 監視器紀錄的整合](../azure-monitor/platform/om-agents.md#validate-operations-manager-integration-with-azure-monitor)。

### <a name="single-azure-vm-assessment-queries-windows"></a>單一 Azure VM 評估查詢 (Windows)

將 VMUUID 值取代為您目前查詢之虛擬機器的 VM GUID。 您可以在 Azure 監視器紀錄中執行以下查詢來尋找此軟體的 VMUUID:`Update | where Computer == "<machine name>" | summarize by Computer, VMUUID`

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

對於某些 Linux 發行版,與來自 Azure 資源管理員的 VMUUID 值和儲存在 Azure 監視器日誌中的內容存在[內質](https://en.wikipedia.org/wiki/Endianness)不匹配。 以下查詢會檢查這上述兩者的位元組順序是否相符。 將 VMUUID 值取代為 GUID 的位元組由大到小和位元組由小到大格式，以正確地傳回結果。 您可以在 Azure 監視器紀錄中執行以下查詢來尋找此軟體的 VMUUID:`Update | where Computer == "<machine name>"
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

* 使用 Azure[監視器日誌](../log-analytics/log-analytics-log-searches.md)中的日誌搜尋詳細的更新數據。
* 針對更新部署狀態[建立警示](automation-tutorial-update-management.md#configure-alerts)。
