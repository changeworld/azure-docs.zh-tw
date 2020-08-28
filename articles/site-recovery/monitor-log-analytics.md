---
title: 使用 Azure 監視器記錄監視 Azure Site Recovery
description: '瞭解如何 (Log Analytics 監視 Azure 監視器記錄的 Azure Site Recovery) '
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/15/2019
ms.author: raynew
ms.openlocfilehash: 7d11fa8605d9cd5f335b6be56097caf7a5222bbd
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2020
ms.locfileid: "89006936"
---
# <a name="monitor-site-recovery-with-azure-monitor-logs"></a>透過 Azure 監視器記錄監視 Site Recovery

本文說明如何使用[Azure 監視器記錄](../azure-monitor/platform/data-platform-logs.md)和[Log Analytics](../azure-monitor/log-query/log-query-overview.md)，來監視 Azure [Site Recovery](site-recovery-overview.md)所複寫的機器。

Azure 監視器記錄檔會提供記錄資料平臺，以收集活動和資源記錄，以及其他監視資料。 在 Azure 監視器記錄中，您可以使用 Log Analytics 來撰寫和測試記錄查詢，以及以互動方式分析記錄資料。 您可以將記錄結果視覺化並進行查詢，並設定警示以根據受監視的資料採取動作。

針對 Site Recovery，您可以使用 Azure 監視器記錄檔來協助您執行下列作業：

- **監視 Site Recovery 健康情況和狀態**。 例如，您可以監視複寫健康情況、測試容錯移轉狀態、Site Recovery 事件、復原點目標 (適用于受保護機器的 Rpo) ，以及磁片/資料變更率。
- **設定 Site Recovery 的警示**。 例如，您可以設定電腦健全狀況、測試容錯移轉狀態或 Site Recovery 作業狀態的警示。

針對 azure **至 azure** 的複寫，以及將 **VMware VM/實體伺服器複寫到 azure** ，都支援搭配使用 Azure 監視器記錄與 Site Recovery。

> [!NOTE]
> 若要取得 VMware 和實體機器的流失資料記錄和上傳速率記錄，您需要在進程伺服器上安裝 Microsoft monitoring agent。 此代理程式會將複寫機器的記錄傳送至工作區。 這項功能僅適用于9.30 的行動代理程式版本。

## <a name="before-you-start"></a>在您開始使用 Intune 之前

以下是所需項目：

- 至少有一部機器在復原服務保存庫中受到保護。
- 用以儲存 Site Recovery 記錄的 Log Analytics 工作區。 [瞭解如何](../azure-monitor/learn/quick-create-workspace.md) 設定工作區。
- 如何在 Log Analytics 中撰寫、執行及分析記錄查詢的基本知識。 [深入了解](../azure-monitor/log-query/get-started-portal.md)。

在開始之前，建議您先複習 [常見的監視問題](monitoring-common-questions.md) 。

## <a name="configure-site-recovery-to-send-logs"></a>設定 Site Recovery 以傳送記錄

1. 在保存庫中，按一下 [**診斷**設定  >  **新增診斷設定**]。

    ![顯示 [新增診斷設定] 選項的螢幕擷取畫面。](./media/monitoring-log-analytics/add-diagnostic.png)

2. 在 [ **診斷設定**] 中指定名稱，然後選取 [ **傳送至 Log Analytics**] 方塊。
3. 選取 [Azure 監視器記錄] 訂用帳戶和 Log Analytics 工作區。
4. 選取切換中的 **Azure 診斷** 。
5. 從記錄清單中，選取前置詞為 **AzureSiteRecovery**的所有記錄。 然後按一下 [確定]  。

    ![[診斷設定] 畫面的螢幕擷取畫面。](./media/monitoring-log-analytics/select-workspace.png)

Site Recovery 記錄開始送入資料表中， (在選取的工作區中使用 **AzureDiagnostics**) 。

## <a name="configure-microsoft-monitoring-agent-on-the-process-server-to-send-churn-and-upload-rate-logs"></a>在進程伺服器上設定 Microsoft monitoring agent，以傳送流失和上傳率記錄

您可以在內部部署中，為您的 VMware/實體機器，捕獲資料變換率資訊和來源資料上傳速率資訊。 若要啟用此功能，必須在進程伺服器上安裝 Microsoft monitoring agent。

1. 移至 Log Analytics 工作區，然後按一下 [ **Advanced Settings**]。
2. 按一下 [ **連接的來源** ] 頁面，然後再選取 [ **Windows server**]。
3. 在進程伺服器上下載 Windows 代理程式 (64 位) 。 
4. [取得工作區識別碼和金鑰](../azure-monitor/platform/log-analytics-agent.md#workspace-id-and-key)
5. [將代理程式設定為使用 TLS 1。2](../azure-monitor/platform/agent-windows.md#configure-agent-to-use-tls-12)
6. 藉由提供取得的工作區識別碼和金鑰來[完成代理程式安裝](../azure-monitor/platform/agent-windows.md#install-agent-using-setup-wizard)。
7. 安裝完成之後，請移至 Log Analytics 工作區，然後按一下 [ **Advanced Settings**]。 移至 [ **資料** ] 頁面，然後再按一下 [ **Windows 效能計數器**]。 
8. 按一下 [ **+** ] 以新增下列兩個取樣間隔為300秒的計數器：

    - ASRAnalytics ( * ) \SourceVmChurnRate
    - ASRAnalytics ( * ) \SourceVmThrpRate

變換和上傳率資料會開始送入工作區。


## <a name="query-the-logs---examples"></a>查詢記錄-範例

您可以使用以 [Kusto 查詢語言](../azure-monitor/log-query/get-started-queries.md)撰寫的記錄查詢，從記錄檔中取出資料。 本節提供一些範例，說明您可能用於 Site Recovery 監視的常見查詢。

> [!NOTE]
> 部分範例使用 **replicationProviderName_s** 設定為 **A2A**。 這會使用 Site Recovery 來抓取複寫至次要 Azure 區域的 Azure Vm。 在這些範例中，如果您想要使用 Site Recovery 來抓取複寫到 Azure 的內部部署 VMware Vm 或實體伺服器，您可以將 **A2A** 取代為 **InMageAzureV2**。


### <a name="query-replication-health"></a>查詢複寫健全狀況

此查詢會繪製圓形圖，以瞭解所有受保護的 Azure Vm 目前的複寫健康情況，分為三種狀態：正常、警告或嚴重。

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

此查詢會繪製使用 Site Recovery 複寫之 Azure Vm 的圓形圖，並依其執行的行動代理程式版本細分。

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

此查詢會繪製使用 Site Recovery 所複寫的 Azure Vm 橫條圖，並依復原點目標 (RPO) ：少於15分鐘，介於15-30 分鐘到30分鐘之間。

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

![顯示以 Site Recovery 複寫的 Azure Vm 橫條圖的螢幕擷取畫面。](./media/monitoring-log-analytics/example1.png)

### <a name="query-site-recovery-jobs"></a>查詢 Site Recovery 作業

此查詢會針對所有的嚴重損壞修復案例，取得所有 Site Recovery 作業 () 、在過去72小時內觸發，以及完成狀態。

```
AzureDiagnostics  
| where Category == "AzureSiteRecoveryJobs"  
| where TimeGenerated >= ago(72h)   
| project JobName = OperationName , VaultName = Resource , TargetName = affectedResourceName_s, State = ResultType  
```

### <a name="query-site-recovery-events"></a>查詢 Site Recovery 事件

此查詢會針對過去72小時內) 引發的所有嚴重損壞修復案例，取得 (的所有 Site Recovery 事件，以及其嚴重性。 

```
AzureDiagnostics   
| where Category == "AzureSiteRecoveryEvents"   
| where TimeGenerated >= ago(72h)   
| project AffectedObject=affectedResourceName_s , VaultName = Resource, Description_s = healthErrors_s , Severity = Level  
```

### <a name="query-test-failover-state-pie-chart"></a>查詢測試容錯移轉狀態 (圓形圖) 

此查詢會繪製以 Site Recovery 複寫的 Azure Vm 測試容錯移轉狀態的圓形圖。

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

### <a name="query-test-failover-state-table"></a>查詢測試容錯移轉狀態 (資料表) 

此查詢會針對使用 Site Recovery 複寫的 Azure Vm，繪製其測試容錯移轉狀態的表格。

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| where isnotempty(failoverHealth_s) and isnotnull(failoverHealth_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project VirtualMachine = name_s , VaultName = Resource , TestFailoverStatus = failoverHealth_s 
```

### <a name="query-machine-rpo"></a>查詢機器 RPO

此查詢會繪製一個趨勢圖，以追蹤過去72小時內特定 Azure VM (ContosoVM123) 的 RPO。

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where TimeGenerated > ago(72h)  
| where isnotempty(name_s) and isnotnull(name_s)   
| where name_s == "ContosoVM123"  
| project TimeGenerated, name_s , RPO_in_seconds = rpoInSeconds_d   
| render timechart 
```
![趨勢圖的螢幕擷取畫面，其中會追蹤特定 Azure VM 的 RPO。](./media/monitoring-log-analytics/example2.png)

### <a name="query-data-change-rate-churn-and-upload-rate-for-an-azure-vm"></a>Azure VM 的查詢資料變更率 (流失) 和上傳率

此查詢會繪製特定 Azure VM (ContosoVM123) 的趨勢圖，表示 (每秒寫入位元組) 和資料上傳速率的資料變更率。 

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
![特定 Azure VM 趨勢圖的螢幕擷取畫面。](./media/monitoring-log-analytics/example3.png)

### <a name="query-data-change-rate-churn-and-upload-rate-for-a-vmware-or-physical-machine"></a>VMware 或實體機器的查詢資料變更率 (變換) 和上傳率

> [!Note]
> 請確定您已在進程伺服器上設定監視代理程式來提取這些記錄。 請參閱 [設定監視代理程式的步驟](#configure-microsoft-monitoring-agent-on-the-process-server-to-send-churn-and-upload-rate-logs)。

此查詢會針對複寫專案的特定磁片 **disk0.vhdx** （ **9r7sfh9qlru**）繪製趨勢圖，表示資料變更率 (每秒寫入位元組數) 和資料上傳速率。 您可以在復原服務保存庫中，找到複寫專案的 [ **磁片** 上的磁片名稱] 分頁。 要在查詢中使用的實例名稱是電腦的 DNS 名稱，後面接著 _ 和磁片名稱，如這個範例所示。

```
Perf
| where ObjectName == "ASRAnalytics"
| where InstanceName contains "win-9r7sfh9qlru_disk0"
| where TimeGenerated >= ago(4h) 
| project TimeGenerated ,CounterName, Churn_MBps = todouble(CounterValue)/5242880 
| render timechart
```
進程伺服器每隔5分鐘就會將此資料推送至 Log Analytics 工作區。 這些資料點代表5分鐘的平均計算。

### <a name="query-disaster-recovery-summary-azure-to-azure"></a> (Azure 至 Azure) 查詢嚴重損壞修復摘要

此查詢會繪製複寫至次要 Azure 區域之 Azure Vm 的摘要資料表。  它會顯示 VM 名稱、複寫和保護狀態、RPO、測試容錯移轉狀態、行動代理程式版本、任何作用中的複寫錯誤，以及來源位置。

```
AzureDiagnostics 
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project VirtualMachine = name_s , Vault = Resource , ReplicationHealth = replicationHealth_s, Status = protectionState_s, RPO_in_seconds = rpoInSeconds_d, TestFailoverStatus = failoverHealth_s, AgentVersion = agentVersion_s, ReplicationError = replicationHealthErrors_s, SourceLocation = primaryFabricName_s 
```

### <a name="query-disaster-recovery-summary-vmwarephysical-servers"></a> (VMware/實體伺服器的查詢嚴重損壞修復摘要) 

此查詢會針對複寫至 Azure 的 VMware Vm 和實體伺服器，繪製摘要資料表。  它會顯示電腦名稱稱、複寫和保護狀態、RPO、測試容錯移轉狀態、行動代理程式版本、任何作用中的複寫錯誤，以及相關的進程伺服器。

```
AzureDiagnostics  
| where replicationProviderName_s == "InMageAzureV2"   
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project VirtualMachine = name_s , Vault = Resource , ReplicationHealth = replicationHealth_s, Status = protectionState_s, RPO_in_seconds = rpoInSeconds_d, TestFailoverStatus = failoverHealth_s, AgentVersion = agentVersion_s, ReplicationError = replicationHealthErrors_s, ProcessServer = processServerName_g  
```

## <a name="set-up-alerts---examples"></a>設定警示-範例

您可以根據 Azure 監視器資料來設定 Site Recovery 警示。 [深入瞭解](../azure-monitor/platform/alerts-log.md#create-a-log-alert-rule-with-the-azure-portal) 設定記錄警示。 

> [!NOTE]
> 部分範例使用 **replicationProviderName_s** 設定為 **A2A**。 這會針對複寫至次要 Azure 區域的 Azure Vm 設定警示。 在這些範例中，如果您想要針對複寫至 Azure 的內部部署 VMware Vm 或實體伺服器設定警示，可以將 **A2A** 取代為 **InMageAzureV2** 。

### <a name="multiple-machines-in-a-critical-state"></a>處於重大狀態的多部機器

如果有超過20個已複寫的 Azure Vm 進入重大狀態，請設定警示。

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where replicationHealth_s == "Critical"  
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count() 
```
針對警示，將 [ **臨界值** ] 設定為20。

### <a name="single-machine-in-a-critical-state"></a>處於重大狀態的單一機器

如果特定的已複寫 Azure VM 進入重大狀態，請設定警示。

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where replicationHealth_s == "Critical"  
| where name_s == "ContosoVM123"  
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count()  
```
針對警示，將 [ **臨界值** ] 設定為1。

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
針對警示，將 [ **臨界值** ] 設定為20。

### <a name="single-machine-exceeds-rpo"></a>單一電腦超過 RPO

如果單一 Azure VM 的 RPO 超過30分鐘，請設定警示。

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
針對警示，將 [ **臨界值** ] 設定為1。

### <a name="test-failover-for-multiple-machines-exceeds-90-days"></a>多部電腦的測試容錯移轉超過90天

如果上一次成功的測試容錯移轉超過90天，請設定警示，超過20部 Vm。 

```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where Category == "AzureSiteRecoveryReplicatedItems"  
| where isnotempty(name_s) and isnotnull(name_s)   
| where lastSuccessfulTestFailoverTime_t <= ago(90d)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count()  
```
針對警示，將 [ **臨界值** ] 設定為20。

### <a name="test-failover-for-single-machine-exceeds-90-days"></a>單一機器的測試容錯移轉超過90天

如果特定 VM 上一次成功的測試容錯移轉超過90天之前，請設定警示。
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
針對警示，將 [ **臨界值** ] 設定為1。

### <a name="site-recovery-job-fails"></a>Site Recovery 作業失敗

如果 Site Recovery 作業 (，請設定警示。在此情況下，在過去一天內的任何 Site Recovery 案例中，重新保護作業) 失敗。 
```
AzureDiagnostics   
| where Category == "AzureSiteRecoveryJobs"   
| where OperationName == "Reprotect"  
| where ResultType == "Failed"  
| summarize count()  
```

針對警示，將 [ **臨界值** ] 設定為1，並將 [ **期間** ] 設為 [1440 分鐘]，以檢查最後一天的失敗次數。

## <a name="next-steps"></a>後續步驟

[瞭解](site-recovery-monitor-and-troubleshoot.md) 內建 Site Recovery 監視。
