---
title: 使用 Azure 監視器記錄監視 Azure Site Recovery
description: 瞭解如何使用 Azure 監視器記錄來監視 Azure Site Recovery （Log Analytics）
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/15/2019
ms.author: raynew
ms.openlocfilehash: 766d0a763f7d69ec58851116e18510235f39b364
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2020
ms.locfileid: "87495058"
---
# <a name="monitor-site-recovery-with-azure-monitor-logs"></a>透過 Azure 監視器記錄監視 Site Recovery

本文說明如何使用[Azure 監視器記錄](../azure-monitor/platform/data-platform-logs.md)和[Log Analytics](../azure-monitor/log-query/log-query-overview.md)來監視 Azure [Site Recovery](site-recovery-overview.md)所複寫的機器。

Azure 監視器記錄檔提供的記錄資料平臺會收集活動和資源記錄，以及其他監視資料。 在 Azure 監視器記錄中，您可以使用 Log Analytics 來撰寫和測試記錄查詢，並以互動方式分析記錄資料。 您可以將記錄結果視覺化並加以查詢，並設定警示以根據受監視的資料採取動作。

針對 Site Recovery，您可以 Azure 監視器記錄檔，以協助您執行下列動作：

- **監視 Site Recovery 健康情況和狀態**。 例如，您可以監視複寫健全狀況、測試容錯移轉狀態、Site Recovery 事件、受保護機器的復原點目標（Rpo），以及磁片/資料變更率。
- **設定 Site Recovery 的警示**。 例如，您可以設定電腦健全狀況、測試容錯移轉狀態或 Site Recovery 作業狀態的警示。

**Azure 至 azure**複寫和**VMware VM/實體伺服器至 azure**的複寫支援搭配使用 Azure 監視器記錄與 Site Recovery。

> [!NOTE]
> 若要取得 VMware 和實體機器的流失資料記錄和上傳速率記錄，您需要在進程伺服器上安裝 Microsoft monitoring agent。 此代理程式會將複寫電腦的記錄傳送至工作區。 這項功能僅適用于9.30 行動代理程式的版本。

## <a name="before-you-start"></a>開始之前

以下是所需項目：

- 復原服務保存庫中至少有一部機器受到保護。
- 用來儲存 Site Recovery 記錄的 Log Analytics 工作區。 [瞭解如何](../azure-monitor/learn/quick-create-workspace.md)設定工作區。
- 如何在 Log Analytics 中撰寫、執行和分析記錄查詢的基本知識。 [深入了解](../azure-monitor/log-query/get-started-portal.md)。

我們建議您在開始之前，先檢查[常見的監控問題](monitoring-common-questions.md)。

## <a name="configure-site-recovery-to-send-logs"></a>設定 Site Recovery 來傳送記錄檔

1. 在保存庫中，按一下 [**診斷設定**] [  >  **新增診斷設定**]。

    ![顯示 [新增診斷設定] 選項的螢幕擷取畫面。](./media/monitoring-log-analytics/add-diagnostic.png)

2. 在 [**診斷設定**] 中指定名稱，然後選取 [**傳送至 Log Analytics**] 方塊。
3. 選取 [Azure 監視器記錄] 訂用帳戶和 Log Analytics 工作區。
4. 在切換中選取 [ **Azure 診斷**]。
5. 從 [記錄檔] 清單中，選取前置詞為**AzureSiteRecovery**的所有記錄檔。 然後按一下 [確定]。

    ![[診斷設定] 畫面的螢幕擷取畫面。](./media/monitoring-log-analytics/select-workspace.png)

Site Recovery 記錄會開始饋送至所選工作區中的資料表（**AzureDiagnostics**）。

## <a name="configure-microsoft-monitoring-agent-on-the-process-server-to-send-churn-and-upload-rate-logs"></a>在進程伺服器上設定 Microsoft monitoring agent，以傳送流失和上傳速率記錄

您可以在內部部署中，針對 VMware/實體機器，捕獲資料變換率資訊和來源資料上傳速率資訊。 若要啟用這種情況，必須在進程伺服器上安裝 Microsoft monitoring agent。

1. 移至 Log Analytics 工作區，然後按一下 [ **Advanced Settings**]。
2. 按一下 [**連線的來源**] 頁面，然後再選取 [ **Windows 伺服器**]。
3. 在進程伺服器上下載 Windows 代理程式（64位）。 
4. [取得工作區識別碼和金鑰](../azure-monitor/platform/agent-windows.md#obtain-workspace-id-and-key)
5. [將代理程式設定為使用 TLS 1。2](../azure-monitor/platform/agent-windows.md#configure-agent-to-use-tls-12)
6. 藉由提供取得的工作區識別碼和金鑰來[完成代理程式安裝](../azure-monitor/platform/agent-windows.md#install-the-agent-using-setup-wizard)。
7. 安裝完成後，請移至 Log Analytics 工作區，然後按一下 [ **Advanced Settings**]。 移至 [**資料**] 頁面，然後再按一下 [ **Windows 效能計數器**]。 
8. 按一下 [ **+** ] 以新增下列兩個取樣間隔為300秒的計數器：

    - ASRAnalytics(*)\SourceVmChurnRate
    - ASRAnalytics(*)\SourceVmThrpRate

變換和上傳速率資料會開始放入工作區中。


## <a name="query-the-logs---examples"></a>查詢記錄-範例

您可以使用以[Kusto 查詢語言](../azure-monitor/log-query/get-started-queries.md)撰寫的記錄查詢，從記錄檔中取出資料。 本節提供一些您可能用於 Site Recovery 監視的常見查詢範例。

> [!NOTE]
> 部分範例會使用**replicationProviderName_s**設定為**A2A**。 這會使用 Site Recovery 來抓取複寫到次要 Azure 區域的 Azure Vm。 在這些範例中，如果您想要使用 Site Recovery 來抓取複寫至 Azure 的內部部署 VMware Vm 或實體伺服器，您可以將**A2A**取代為**InMageAzureV2**。


### <a name="query-replication-health"></a>查詢複寫健全狀況

此查詢會針對所有受保護 Azure Vm 的目前複寫健全狀況繪製圓形圖，並分成三個狀態： [一般]、[警告] 或 [重大]。

```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s  
| project name_s , replicationHealth_s  
| summarize count() by replicationHealth_s  
| render piechart   
```
### <a name="query-mobility-service-version"></a>查詢行動服務版本

此查詢會繪製以 Site Recovery 進行複寫之 Azure Vm 的圓形圖，並依所執行的行動代理程式版本加以細分。

```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s  
| project name_s , agentVersion_s  
| summarize count() by agentVersion_s  
| render piechart 
```

### <a name="query-rpo-time"></a>查詢 RPO 時間

此查詢會繪製以 Site Recovery 複寫的 Azure Vm 橫條圖，並依復原點目標（RPO）細分：小於15分鐘，介於15-30 分鐘到30分鐘之間。

```
AzureDiagnostics 
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)  
| extend RPO = case(rpoInSeconds_d <= 900, "<15Min",   
rpoInSeconds_d <= 1800, "15-30Min", ">30Min")  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s  
| project name_s , RPO  
| summarize Count = count() by RPO  
| render barchart 
```

![螢幕擷取畫面，顯示以 Site Recovery 複寫的 Azure Vm 橫條圖。](./media/monitoring-log-analytics/example1.png)

### <a name="query-site-recovery-jobs"></a>查詢 Site Recovery 作業

此查詢會抓取所有 Site Recovery 作業（適用于所有嚴重損壞修復案例），並在過去72小時內觸發，以及完成狀態。

```
AzureDiagnostics  
| where Category == "AzureSiteRecoveryJobs"  
| where TimeGenerated >= ago(72h)   
| project JobName = OperationName , VaultName = Resource , TargetName = affectedResourceName_s, State = ResultType  
```

### <a name="query-site-recovery-events"></a>查詢 Site Recovery 事件

此查詢會抓取過去72小時內引發的所有 Site Recovery 事件（適用于所有嚴重損壞修復案例）及其嚴重性。 

```
AzureDiagnostics   
| where Category == "AzureSiteRecoveryEvents"   
| where TimeGenerated >= ago(72h)   
| project AffectedObject=affectedResourceName_s , VaultName = Resource, Description_s = healthErrors_s , Severity = Level  
```

### <a name="query-test-failover-state-pie-chart"></a>查詢測試容錯移轉狀態（圓形圖）

此查詢會繪製以 Site Recovery 複寫之 Azure Vm 的測試容錯移轉狀態圓形圖。

```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)  
| where isnotempty(failoverHealth_s) and isnotnull(failoverHealth_s)  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s  
| project name_s , Resource, failoverHealth_s  
| summarize count() by failoverHealth_s  
| render piechart 
```

### <a name="query-test-failover-state-table"></a>查詢測試容錯移轉狀態（資料表）

此查詢會針對以 Site Recovery 複寫的 Azure Vm 的測試容錯移轉狀態繪製資料表。

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| where isnotempty(failoverHealth_s) and isnotnull(failoverHealth_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project VirtualMachine = name_s , VaultName = Resource , TestFailoverStatus = failoverHealth_s 
```

### <a name="query-machine-rpo"></a>查詢機器 RPO

此查詢會繪製一個趨勢圖，以追蹤過去72小時內特定 Azure VM （ContosoVM123）的 RPO。

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where TimeGenerated > ago(72h)  
| where isnotempty(name_s) and isnotnull(name_s)   
| where name_s == "ContosoVM123"  
| project TimeGenerated, name_s , RPO_in_seconds = rpoInSeconds_d   
| render timechart 
```
![追蹤特定 Azure VM RPO 的趨勢圖表螢幕擷取畫面。](./media/monitoring-log-analytics/example2.png)

### <a name="query-data-change-rate-churn-and-upload-rate-for-an-azure-vm"></a>查詢 Azure VM 的資料變更率（變換）和上傳速率

此查詢會繪製特定 Azure VM （ContosoVM123）的趨勢圖，其代表資料變更率（每秒寫入位元組）和資料上傳速率。 

```
AzureDiagnostics   
| where Category in ("AzureSiteRecoveryProtectedDiskDataChurn", "AzureSiteRecoveryReplicationDataUploadRate")   
| extend CategoryS = case(Category contains "Churn", "DataChurn",   
Category contains "Upload", "UploadRate", "none")  
| extend InstanceWithType=strcat(CategoryS, "_", InstanceName_s)   
| where TimeGenerated > ago(24h)   
| where InstanceName_s startswith "ContosoVM123"   
| project TimeGenerated , InstanceWithType , Churn_MBps = todouble(Value_s)/1048576   
| render timechart  
```
![特定 Azure VM 的趨勢圖表螢幕擷取畫面。](./media/monitoring-log-analytics/example3.png)

### <a name="query-data-change-rate-churn-and-upload-rate-for-a-vmware-or-physical-machine"></a>查詢 VMware 或實體機器的資料變更率（變換）和上傳速率

> [!Note]
> 請確定您已在進程伺服器上設定監視代理程式，以提取這些記錄檔。 請參閱[設定監視代理程式的步驟](#configure-microsoft-monitoring-agent-on-the-process-server-to-send-churn-and-upload-rate-logs)。

此查詢會繪製**9r7sfh9qlru**複寫專案之特定磁片**disk0.vhdx**的趨勢圖，其代表資料變更率（每秒寫入位元組）和資料上傳速率。 您可以在復原服務保存庫中，于複寫專案的 [**磁片**] 分頁上找到磁片名稱。 要在查詢中使用的實例名稱是電腦的 DNS 名稱，後面接著 _ 和磁片名稱，如下列範例所示。

```
Perf
| where ObjectName == "ASRAnalytics"
| where InstanceName contains "win-9r7sfh9qlru_disk0"
| where TimeGenerated >= ago(4h) 
| project TimeGenerated ,CounterName, Churn_MBps = todouble(CounterValue)/5242880 
| render timechart
```
進程伺服器會每隔5分鐘將此資料推送至 Log Analytics 工作區。 這些資料點代表5分鐘計算的平均值。

### <a name="query-disaster-recovery-summary-azure-to-azure"></a>查詢嚴重損壞修復摘要（Azure 至 Azure）

此查詢會針對複寫到次要 Azure 區域的 Azure Vm 繪製摘要資料表。  它會顯示 VM 名稱、複寫和保護狀態、RPO、測試容錯移轉狀態、行動代理程式版本、任何作用中的複寫錯誤，以及來源位置。

```
AzureDiagnostics 
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project VirtualMachine = name_s , Vault = Resource , ReplicationHealth = replicationHealth_s, Status = protectionState_s, RPO_in_seconds = rpoInSeconds_d, TestFailoverStatus = failoverHealth_s, AgentVersion = agentVersion_s, ReplicationError = replicationHealthErrors_s, SourceLocation = primaryFabricName_s 
```

### <a name="query-disaster-recovery-summary-vmwarephysical-servers"></a>查詢嚴重損壞修復摘要（VMware/實體伺服器）

此查詢會針對複寫到 Azure 的 VMware Vm 和實體伺服器繪製摘要資料表。  它會顯示電腦名稱稱、複寫和保護狀態、RPO、測試容錯移轉狀態、行動代理程式版本、任何作用中的複寫錯誤，以及相關的進程伺服器。

```
AzureDiagnostics  
| where replicationProviderName_s == "InMageAzureV2"   
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project VirtualMachine = name_s , Vault = Resource , ReplicationHealth = replicationHealth_s, Status = protectionState_s, RPO_in_seconds = rpoInSeconds_d, TestFailoverStatus = failoverHealth_s, AgentVersion = agentVersion_s, ReplicationError = replicationHealthErrors_s, ProcessServer = processServerName_g  
```

## <a name="set-up-alerts---examples"></a>設定警示-範例

您可以根據 Azure 監視器資料來設定 Site Recovery 警示。 [深入瞭解](../azure-monitor/platform/alerts-log.md#create-a-log-alert-rule-with-the-azure-portal)如何設定記錄警示。 

> [!NOTE]
> 部分範例會使用**replicationProviderName_s**設定為**A2A**。 這會針對複寫到次要 Azure 區域的 Azure Vm 設定警示。 在這些範例中，如果您想要設定複寫至 Azure 的內部部署 VMware Vm 或實體伺服器的警示，您可以將**A2A**取代為**InMageAzureV2** 。

### <a name="multiple-machines-in-a-critical-state"></a>處於重大狀態的多部電腦

如果有超過20個複寫的 Azure Vm 進入重大狀態，請設定警示。

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where replicationHealth_s == "Critical"  
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count() 
```
針對警示，將 [**臨界值**] 設定為20。

### <a name="single-machine-in-a-critical-state"></a>處於重大狀態的單一電腦

如果特定複寫的 Azure VM 進入重大狀態，請設定警示。

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where replicationHealth_s == "Critical"  
| where name_s == "ContosoVM123"  
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count()  
```
針對警示，將 [**臨界值**] 設定為1。

### <a name="multiple-machines-exceed-rpo"></a>多部機器超過 RPO

如果超過20個 Azure Vm 的 RPO 超過30分鐘，請設定警示。
```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| where rpoInSeconds_d > 1800  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project name_s , rpoInSeconds_d   
| summarize count()  
```
針對警示，將 [**臨界值**] 設定為20。

### <a name="single-machine-exceeds-rpo"></a>單一機器超過 RPO

設定單一 Azure VM 的 RPO 超過30分鐘時發出警示。

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| where name_s == "ContosoVM123"  
| where rpoInSeconds_d > 1800  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project name_s , rpoInSeconds_d   
| summarize count()  
```
針對警示，將 [**臨界值**] 設定為1。

### <a name="test-failover-for-multiple-machines-exceeds-90-days"></a>多部機器的測試容錯移轉超過90天

如果上一次成功的測試容錯移轉超過90天，則針對20部以上的 Vm 設定警示。 

```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where Category == "AzureSiteRecoveryReplicatedItems"  
| where isnotempty(name_s) and isnotnull(name_s)   
| where lastSuccessfulTestFailoverTime_t <= ago(90d)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count()  
```
針對警示，將 [**臨界值**] 設定為20。

### <a name="test-failover-for-single-machine-exceeds-90-days"></a>單一機器的測試容錯移轉超過90天

如果特定 VM 上一次成功的測試容錯移轉超過90天前，請設定警示。
```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where Category == "AzureSiteRecoveryReplicatedItems"  
| where isnotempty(name_s) and isnotnull(name_s)   
| where lastSuccessfulTestFailoverTime_t <= ago(90d)   
| where name_s == "ContosoVM123"  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count()  
```
針對警示，將 [**臨界值**] 設定為1。

### <a name="site-recovery-job-fails"></a>Site Recovery 作業失敗

在過去一天內的任何 Site Recovery 案例中，如果 Site Recovery 作業（在此情況下為重新保護作業）失敗，請設定警示。 
```
AzureDiagnostics   
| where Category == "AzureSiteRecoveryJobs"   
| where OperationName == "Reprotect"  
| where ResultType == "Failed"  
| summarize count()  
```

針對警示，請將 [**臨界值**] 設為1，並將 [**週期**] 設定為1440分鐘，以檢查過去一天的失敗。

## <a name="next-steps"></a>後續步驟

[瞭解](site-recovery-monitor-and-troubleshoot.md)內建 Site Recovery 監視。
