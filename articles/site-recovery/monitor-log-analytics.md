---
title: 使用 Azure 監視器日誌監視 Azure 網站恢復
description: 瞭解如何使用 Azure 監視器日誌監視 Azure 網站恢復（日誌分析）
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/15/2019
ms.author: raynew
ms.openlocfilehash: f20d0d38a7fbd831d3e97a69373bac04b9b330aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74133411"
---
# <a name="monitor-site-recovery-with-azure-monitor-logs"></a>透過 Azure 監視器記錄監視 Site Recovery

本文介紹如何使用[Azure 監視器日誌](../azure-monitor/platform/data-platform-logs.md)和[日誌分析](../azure-monitor/log-query/log-query-overview.md)監視 Azure[網站恢復](site-recovery-overview.md)複製的電腦。

Azure 監視器日誌提供一個日誌資料平臺，用於收集活動和診斷日誌以及其他監視資料。 在 Azure 監視器日誌中，使用日誌分析編寫和測試日誌查詢，以及互動式分析日誌資料。 您可以視覺化和查詢日誌結果，並將警報配置為根據受監視的資料執行操作。

對於網站恢復，您可以 Azure 監視器日誌説明您執行以下操作：

- **監視網站恢復運行狀況和狀態**。 例如，您可以監視複製運行狀況、測試容錯移轉狀態、網站恢復事件、受保護電腦的復原點目標 （RPO） 以及磁片/資料更改率。
- **為網站恢復設置警報**。 例如，您可以為電腦運行狀況、測試容錯移轉狀態或網站恢復作業狀態配置警報。

Azure 到**Azure**複製使用 Azure 監視器日誌與網站恢復 **，VMware VM/物理伺服器對 Azure**複製支援。

> [!NOTE]
> 要獲取 VMware 和物理電腦的改動資料日誌和上載速率日誌，需要在進程伺服器上安裝 Microsoft 監視代理。 此代理將複製電腦的日誌發送到工作區。 此功能僅適用于 9.30 移動代理版本。

## <a name="before-you-start"></a>開始之前

以下是所需項目：

- 恢復服務保存庫中至少有一台電腦受到保護。
- 用於存儲網站修復記錄的日誌分析工作區。 [瞭解如何](../azure-monitor/learn/quick-create-workspace.md)設置工作區。
- 基本瞭解如何在日誌分析中寫入、運行和分析日誌查詢。 [深入了解](../azure-monitor/log-query/get-started-portal.md)。

我們建議您在開始之前查看[常見的監視問題](monitoring-common-questions.md)。

## <a name="configure-site-recovery-to-send-logs"></a>將網站恢復配置為發送日誌

1. 在保存庫中，按一下 **"診斷設置** > **添加診斷設置**"。

    ![選擇診斷日誌記錄](./media/monitoring-log-analytics/add-diagnostic.png)

2. 在**診斷設置**中，指定名稱，然後選中"**發送到日誌分析**"核取方塊。
3. 選擇 Azure 監視器日誌訂閱和日誌分析工作區。
4. 在切換中選擇**Azure 診斷**。
5. 從日誌清單中，選擇具有首碼**AzureSite 恢復**的所有日誌。 然後按一下 **[確定]**。

    ![選取工作區](./media/monitoring-log-analytics/select-workspace.png)

網站修復記錄開始饋送到所選工作區中的表 （**Azure 診斷**）。

## <a name="configure-microsoft-monitoring-agent-on-the-process-server-to-send-churn-and-upload-rate-logs"></a>在進程伺服器上配置 Microsoft 監視代理以發送改動和上載速率日誌

您可以捕獲本地 VMware/物理電腦的資料改動率資訊和來源資料上傳速率資訊。 為此，需要在進程伺服器上安裝 Microsoft 監視代理。

1. 轉到日誌分析工作區，然後按一下 **"高級設置**"。
2. 按一下 **"已連接源"** 頁面，然後進一步選擇**Windows 伺服器**。
3. 在進程伺服器上下載 Windows 代理（64 位）。 
4. [獲取工作區 ID 和金鑰](../azure-monitor/platform/agent-windows.md#obtain-workspace-id-and-key)
5. [配置代理以使用 TLS 1.2](../azure-monitor/platform/agent-windows.md#configure-agent-to-use-tls-12)
6. 通過提供獲取的工作區 ID 和金鑰[來完成代理安裝](../azure-monitor/platform/agent-windows.md#install-the-agent-using-setup-wizard)。
7. 安裝完成後，轉到日誌分析工作區，然後按一下 **"高級設置**"。 轉到 **"資料"** 頁，然後進一步按一下**Windows 效能計數器**。 
8. 按一下 **"+"** 添加以下兩個計數器，取樣間隔為 300 秒：

        ASRAnalytics(*)\SourceVmChurnRate 
        ASRAnalytics(*)\SourceVmThrpRate 

改動率和上載速率資料將開始饋送到工作區中。


## <a name="query-the-logs---examples"></a>查詢日誌 - 示例

您可以使用使用[Kusto 查詢語言](../azure-monitor/log-query/get-started-queries.md)編寫的日誌查詢從日誌中檢索資料。 本節提供了可用於網站恢復監視的常見查詢的幾個示例。

> [!NOTE]
> 某些示例使用**replicationProviderName_s**設置為**A2A。** 這將檢索使用網站恢復複製到輔助 Azure 區域的 Azure VM。 在這些示例中，如果要檢索使用網站恢復複製到 Azure 的本地 VMware VM 或物理伺服器，則可以將**A2A**替換為**InMageAzureV2。**


### <a name="query-replication-health"></a>查詢複製運行狀況

此查詢為所有受保護的 Azure VM 的當前複製運行狀況繪製圓形圖，細分為三種狀態：正常、警告或嚴重狀態。

```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s  
| project name_s , replicationHealth_s  
| summarize count() by replicationHealth_s  
| render piechart   
```
### <a name="query-mobility-service-version"></a>查詢移動服務版本

此查詢為使用網站恢復複製的 Azure VM 繪製圓形圖，按正在運行的移動代理版本細分。

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

此查詢繪製了與網站恢復一起複製的 Azure VM 的橫條圖，按復原點目標 （RPO） 細分：不到 15 分鐘，15-30 分鐘，超過 30 分鐘。

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

![查詢 RPO](./media/monitoring-log-analytics/example1.png)

### <a name="query-site-recovery-jobs"></a>查詢網站恢復作業

此查詢檢索過去 72 小時內觸發的所有網站恢復作業（對於所有災害復原方案）及其完成狀態。

```
AzureDiagnostics  
| where Category == "AzureSiteRecoveryJobs"  
| where TimeGenerated >= ago(72h)   
| project JobName = OperationName , VaultName = Resource , TargetName = affectedResourceName_s, State = ResultType  
```

### <a name="query-site-recovery-events"></a>查詢網站恢復事件

此查詢檢索過去 72 小時內引發的所有網站恢復事件（對於所有災害復原方案），以及其嚴重性。 

```
AzureDiagnostics   
| where Category == "AzureSiteRecoveryEvents"   
| where TimeGenerated >= ago(72h)   
| project AffectedObject=affectedResourceName_s , VaultName = Resource, Description_s = healthErrors_s , Severity = Level  
```

### <a name="query-test-failover-state-pie-chart"></a>查詢測試容錯移轉狀態（圓形圖）

此查詢為使用網站恢復複製的 Azure VM 的測試容錯移轉狀態繪製圓形圖。

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

### <a name="query-test-failover-state-table"></a>查詢測試容錯移轉狀態（表）

此查詢為使用網站恢復複製的 Azure VM 的測試容錯移轉狀態繪製表。

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| where isnotempty(failoverHealth_s) and isnotnull(failoverHealth_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project VirtualMachine = name_s , VaultName = Resource , TestFailoverStatus = failoverHealth_s 
```

### <a name="query-machine-rpo"></a>查詢機 RPO

此查詢繪製的趨勢圖，該趨勢圖跟蹤特定 Azure VM （ContosoVM123） 過去 72 小時的 RPO。

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where TimeGenerated > ago(72h)  
| where isnotempty(name_s) and isnotnull(name_s)   
| where name_s == "ContosoVM123"  
| project TimeGenerated, name_s , RPO_in_seconds = rpoInSeconds_d   
| render timechart 
```
![查詢機 RPO](./media/monitoring-log-analytics/example2.png)

### <a name="query-data-change-rate-churn-and-upload-rate-for-an-azure-vm"></a>查詢 Azure VM 的資料更改率（轉換）和上載速率

此查詢為特定的 Azure VM（ContosoVM123）繪製趨勢圖，表示資料更改速率（每秒寫入位元組數）和資料上載速率。 

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
![查詢資料更改](./media/monitoring-log-analytics/example3.png)

### <a name="query-data-change-rate-churn-and-upload-rate-for-a-vmware-or-physical-machine"></a>VMware 或物理電腦的查詢資料更改率（流失）和上載速率

> [!Note]
> 請確保在進程伺服器上設置監視代理以獲取這些日誌。 請參閱[配置監視代理的步驟](#configure-microsoft-monitoring-agent-on-the-process-server-to-send-churn-and-upload-rate-logs)。

此查詢為複製專案**win-9r7sfh9qlru**的特定**磁片 0**繪製趨勢圖，該圖表示資料更改速率（每秒寫入位元組數）和資料上載速率。 您可以在恢復服務保存庫中複製專案的**磁片**邊欄選項卡上找到磁片名稱。 要在查詢中使用的實例名稱是電腦的 DNS 名稱，後跟 * 和磁片名稱，如本示例所示。

```
Perf
| where ObjectName == "ASRAnalytics"
| where InstanceName contains "win-9r7sfh9qlru_disk0"
| where TimeGenerated >= ago(4h) 
| project TimeGenerated ,CounterName, Churn_MBps = todouble(CounterValue)/5242880 
| render timechart
```
進程伺服器每 5 分鐘將此資料推送到日誌分析工作區。 這些資料點表示 5 分鐘計算的平均值。

### <a name="query-disaster-recovery-summary-azure-to-azure"></a>查詢災害復原摘要（Azure 到 Azure）

此查詢為複製到輔助 Azure 區域的 Azure VM 繪製摘要表。  它顯示 VM 名稱、複製和保護狀態、RPO、測試容錯移轉狀態、移動代理版本、任何活動複製錯誤以及源位置。

```
AzureDiagnostics 
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project VirtualMachine = name_s , Vault = Resource , ReplicationHealth = replicationHealth_s, Status = protectionState_s, RPO_in_seconds = rpoInSeconds_d, TestFailoverStatus = failoverHealth_s, AgentVersion = agentVersion_s, ReplicationError = replicationHealthErrors_s, SourceLocation = primaryFabricName_s 
```

### <a name="query-disaster-recovery-summary-vmwarephysical-servers"></a>查詢災害復原摘要（VMware/物理伺服器）

此查詢為 VMware VM 和複製到 Azure 的物理伺服器繪製摘要表。  它顯示電腦名稱稱、複製和保護狀態、RPO、測試容錯移轉狀態、移動代理版本、任何活動複製錯誤以及相關的進程伺服器。

```
AzureDiagnostics  
| where replicationProviderName_s == "InMageAzureV2"   
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project VirtualMachine = name_s , Vault = Resource , ReplicationHealth = replicationHealth_s, Status = protectionState_s, RPO_in_seconds = rpoInSeconds_d, TestFailoverStatus = failoverHealth_s, AgentVersion = agentVersion_s, ReplicationError = replicationHealthErrors_s, ProcessServer = processServerName_g  
```

## <a name="set-up-alerts---examples"></a>設置警報 - 示例

您可以根據 Azure 監視器資料佈建網站恢復警報。 [詳細瞭解](../azure-monitor/platform/alerts-log.md#managing-log-alerts-from-the-azure-portal)如何設置日誌警報。 

> [!NOTE]
> 某些示例使用**replicationProviderName_s**設置為**A2A。** 這將為複製到輔助 Azure 區域的 Azure VM 設置警報。 在這些示例中，如果要為本地 VMware VM 或複製到 Azure 的物理伺服器設置警報，則可以將**A2A**替換為**InMageAzureV2。**

### <a name="multiple-machines-in-a-critical-state"></a>多台電腦處於臨界狀態

如果超過 20 個複製的 Azure VM 進入嚴重狀態，則設置警報。

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where replicationHealth_s == "Critical"  
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count() 
```
對於警報，將**閾值**設置為 20。

### <a name="single-machine-in-a-critical-state"></a>處於關鍵狀態的單台電腦

如果特定的複製 Azure VM 進入嚴重狀態，則設置警報。

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where replicationHealth_s == "Critical"  
| where name_s == "ContosoVM123"  
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count()  
```
對於警報，將**閾值**設置為 1。

### <a name="multiple-machines-exceed-rpo"></a>多台機器超過 RPO

如果 RPO 超過 20 個 Azure VM 超過 30 分鐘，則設置警報。
```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| where rpoInSeconds_d > 1800  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project name_s , rpoInSeconds_d   
| summarize count()  
```
對於警報，將**閾值**設置為 20。

### <a name="single-machine-exceeds-rpo"></a>單台機器超過 RPO

如果單個 Azure VM 的 RPO 超過 30 分鐘，則設置警報。

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
對於警報，將**閾值**設置為 1。

### <a name="test-failover-for-multiple-machines-exceeds-90-days"></a>多台電腦的測試容錯移轉超過 90 天

如果上次成功的測試容錯移轉超過 90 天，則為 20 多個 VM 設置警報。 

```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where Category == "AzureSiteRecoveryReplicatedItems"  
| where isnotempty(name_s) and isnotnull(name_s)   
| where lastSuccessfulTestFailoverTime_t <= ago(90d)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count()  
```
對於警報，將**閾值**設置為 20。

### <a name="test-failover-for-single-machine-exceeds-90-days"></a>單台電腦的測試容錯移轉超過 90 天

如果特定 VM 的最後一次成功測試容錯移轉是在 90 天前，則設置警報。
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
對於警報，將**閾值**設置為 1。

### <a name="site-recovery-job-fails"></a>網站恢復作業失敗

如果網站恢復作業（在本例中為"重新保護作業"）在最後一天內因任何網站恢復方案失敗，則設置警報。 
```
AzureDiagnostics   
| where Category == "AzureSiteRecoveryJobs"   
| where OperationName == "Reprotect"  
| where ResultType == "Failed"  
| summarize count()  
```

對於警報，將**閾值**設置為 1，**將"期間"** 設置為 1440 分鐘，以檢查最後一天的故障。

## <a name="next-steps"></a>後續步驟

[瞭解](site-recovery-monitor-and-troubleshoot.md)內置網站恢復監視。
