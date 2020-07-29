---
title: Azure Analysis Services 的診斷記錄 | Microsoft Docs
description: 說明如何設定記錄來監視 Azure Analysis Services 伺服器。
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 7e1eab20a8e315b977c21de46dd4f6ea2fec9f5d
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/20/2020
ms.locfileid: "83701485"
---
# <a name="setup-diagnostic-logging"></a>設定診斷記錄

在任何 Analysis Services 解決方案中，監視您伺服器的執行方式皆是一大重點功能。 Azure Analysis Services 與 Azure 監視器整合。 透過 [Azure 監視器資源記錄](../azure-monitor/platform/platform-logs-overview.md)，您可以監視記錄並傳送至 [Azure 儲存體](https://azure.microsoft.com/services/storage/)、將記錄串流至 [Azure 事件中樞](https://azure.microsoft.com/services/event-hubs/)，以及將記錄匯出至 [Azure 監視器記錄](../azure-monitor/azure-monitor-log-hub.md)。

![資源記錄至儲存體、事件中樞或 Azure 監視器記錄](./media/analysis-services-logging/aas-logging-overview.png)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="whats-logged"></a>記錄的內容？

您可以選取 [引擎]、[服務]，和 [計量] 類別。

### <a name="engine"></a>引擎

選取 [引擎] 記錄所有 [xEvents](https://docs.microsoft.com/analysis-services/instances/monitor-analysis-services-with-sql-server-extended-events)。 您無法選取個別事件。 

|XEvent 類別 |事件名稱  |
|---------|---------|
|安全性稽核    |   稽核登入      |
|安全性稽核    |   稽核登出      |
|安全性稽核    |   稽核伺服器啟動與停止      |
|進度報告     |   進度報告開始      |
|進度報告     |   進度報告結束      |
|進度報告     |   進度報告目前狀態      |
|查詢     |  查詢開始       |
|查詢     |   查詢結束      |
|命令     |  命令開始       |
|命令     |  命令結束       |
|錯誤與警告     |   錯誤      |
|探索     |   探索結束      |
|通知     |    通知     |
|工作階段     |  工作階段初始化       |
|鎖定    |  鎖死       |
|查詢處理     |   VertiPaq SE 查詢開始      |
|查詢處理     |   VertiPaq SE 查詢結束      |
|查詢處理     |   VertiPaq SE 查詢快取比對      |
|查詢處理     |   直接查詢開始      |
|查詢處理     |  直接查詢結束       |

### <a name="service"></a>服務

|作業名稱  |發生條件  |
|---------|---------|
|ResumeServer     |    恢復伺服器     |
|SuspendServer    |   暫停伺服器      |
|DeleteServer     |    刪除伺服器     |
|RestartServer    |     使用者透過 SSMS 或 PowerShell 重新啟動伺服器    |
|GetServerLogFiles    |    使用者透過 PowerShell 匯出伺服器記錄     |
|ExportModel     |   使用者使用 [在 Visual Studio 中開啟]，在入口網站中匯出模型     |

### <a name="all-metrics"></a>所有計量

「計量」類別會將相同的[伺服器計量](analysis-services-monitor.md#server-metrics)記錄至 AzureMetrics 資料表。 如果您使用查詢[向外延展](analysis-services-scale-out.md)，而且需要區別每個讀取複本的計量，請改用 AzureDiagnostics 資料表，其中 **OperationName** 等於 **LogMetric**。

## <a name="setup-diagnostics-logging"></a>設定診斷記錄

### <a name="azure-portal"></a>Azure 入口網站

1. 在 [Azure 入口網站](https://portal.azure.com) > 伺服器中，在左側瀏覽中按一下 [診斷記錄]，然後按一下 [開啟診斷]。

    ![在 Azure 入口網站中開啟 Azure Cosmos DB 的資源記錄](./media/analysis-services-logging/aas-logging-turn-on-diagnostics.png)

2. 在 [診斷設定] 中，指定下列選項： 

    * **名稱**。 輸入要建立之記錄的名稱。

    * **封存至儲存體帳戶**。 若要使用此選項，您需要可以連接的現有儲存體帳戶。 請參閱[建立儲存體帳戶](../storage/common/storage-create-storage-account.md)。 請依照指示建立資源管理員、一般用途的帳戶，然後在返回入口網站上的此頁面時，選取您的儲存體帳戶。 新建立的儲存體帳戶可能在數分鐘後才會出現在下拉式功能表中。
    * **串流處理至事件中樞**。 若要使用此選項，您需要可以連接的現有事件中樞命名空間和事件中樞。 若想深入了解，請參閱[使用 Azure 入口網站來建立事件中樞命名空間和事件中樞](../event-hubs/event-hubs-create.md)。 然後返回入口網站的此頁面選取事件中樞命名空間和原則名稱。
    * **傳送至 Azure 監視器 (Log Analytics 工作區)** 。 若要使用此選項，請使用現有的工作區，或是在入口網站中[建立新的工作區](../azure-monitor/learn/quick-create-workspace.md)資源。 如需有關如何檢視記錄的詳細資訊，請參閱本文中的[檢視 Log Analytics 工作區中的記錄](#view-logs-in-log-analytics-workspace)。

    * **引擎**。 選取此選項可記錄 xEvents。 如果您要封存至儲存體帳戶，您可以為資源記錄選取保留期間。 保留期限過後，就會自動刪除記錄。
    * **服務**。 選取此選項可記錄服務層級事件。 如果您要封存至儲存體帳戶，您可以為資源記錄選取保留期間。 保留期限過後，就會自動刪除記錄。
    * **計量**。 選取此選項可儲存[計量](analysis-services-monitor.md#server-metrics)中的詳細資料。 如果您要封存至儲存體帳戶，您可以為資源記錄選取保留期間。 保留期限過後，就會自動刪除記錄。

3. 按一下 [檔案] 。

    如果您收到錯誤，指出「無法更新 \<工作區名稱> 的診斷。 訂用帳戶 \<訂用帳戶識別碼> 未註冊為使用 microsoft.insights」， 請遵循[針對 Azure 診斷進行疑難排解](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage)的指示註冊帳戶，然後重試此程序。

    未來無論何時，如果想要變更資源記錄的儲存方式，您可以返回此頁面來修改設定。

### <a name="powershell"></a>PowerShell

以下基本命令將引導您進行後續作業。 如果您需要逐步說明，了解如何使用 PowerShell 將記錄設定到儲存體帳戶，請參閱本文後段中的教學課程。

若要使用 PowerShell 啟用計量和資源記錄，請使用下列命令：

- 若要將資源記錄儲存在儲存體帳戶中，請使用此命令︰

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
   ```

   儲存體帳戶識別碼是您要傳送記錄之目標儲存體帳戶的資源識別碼。

- 若要將資源記錄串流至事件中樞，請使用此命令︰

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
   ```

   Azure 服務匯流排規則識別碼是此格式的字串︰

   ```powershell
   {service bus resource ID}/authorizationrules/{key name}
   ``` 

- 若要將資源記錄傳送至 Log Analytics 工作區，請使用此命令：

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
   ```

- 您可以使用下列命令取得 Log Analytics 工作區的資源識別碼：

   ```powershell
   (Get-AzOperationalInsightsWorkspace).ResourceId
   ```

您可以結合這些參數讓多個輸出選項。

### <a name="rest-api"></a>REST API

了解如何[使用 Azure 監視器 REST API 變更診斷設定](https://docs.microsoft.com/rest/api/monitor/)。 

### <a name="resource-manager-template"></a>Resource Manager 範本

了解如何[使用 Resource Manager 範本在建立資源時啟用診斷設定](../azure-monitor/platform/diagnostic-settings-template.md)。 

## <a name="manage-your-logs"></a>管理您的記錄

記錄通常會在設定記錄時幾個小時內提供。 儲存體帳戶中的記錄由您全權管理：

* 請使用標準的 Azure 存取控制方法限制可存取記錄的人員，藉此來保護記錄。
* 刪除不想繼續保留在儲存體帳戶中的記錄。
* 請務必設定保留期限，以便從儲存體帳戶刪除舊的記錄。

## <a name="view-logs-in-log-analytics-workspace"></a>檢視 Log Analytics 工作區中的記錄

計量和伺服器事件會與您 Log Analytics 工作區中的 xEvents 整合，以進行並列分析。 您也可以將 Log Analytics 工作區設定為接收來自其他 Azure 服務的事件，以提供整個架構之診斷記錄資料的整體檢視。

若要檢視診斷資料，請開啟 Log Analytics 工作區左側功能表中的 [記錄]。

![Azure 入口網站中的記錄搜尋選項](./media/analysis-services-logging/aas-logging-open-log-search.png)

在查詢產生器中，展開 [LogManagement] > [AzureDiagnostics]。 AzureDiagnostics 包括「引擎」和「服務」事件。 留意到查詢是即時建立的。 EventClass\_s 的欄位包含 xEvent 名稱，如果您曾經使用 xEvents 進行內部部署記錄，這些名稱就可能看起來似曾相似。 按一下 [EventClass\_s] 或其中一個事件名稱，Log Analytics 工作區將會繼續建構查詢。 請務必儲存您的查詢，以供日後重複使用。

### <a name="example-queries"></a>查詢範例

#### <a name="example-1"></a>範例 1

下列查詢針對模型資料庫和伺服器的每個查詢結束/重新整理結束事件，傳回事件的持續時間。 如果已向外延展，因為 ServerName_s 中包含複本編號，將會依複本來細分結果。 依 RootActivityId_g 將取自於 Azure 診斷 REST API 的資料列計數分組，並協助維持在 [Log Analytics 速率限制](https://dev.loganalytics.io/documentation/Using-the-API/Limits)所述的限制內。

```Kusto
let window = AzureDiagnostics
   | where ResourceProvider == "MICROSOFT.ANALYSISSERVICES" and Resource =~ "MyServerName" and DatabaseName_s =~ "MyDatabaseName" ;
window
| where OperationName has "QueryEnd" or (OperationName has "CommandEnd" and EventSubclass_s == 38)
| where extract(@"([^,]*)", 1,Duration_s, typeof(long)) > 0
| extend DurationMs=extract(@"([^,]*)", 1,Duration_s, typeof(long))
| project  StartTime_t,EndTime_t,ServerName_s,OperationName,RootActivityId_g,TextData_s,DatabaseName_s,ApplicationName_s,Duration_s,EffectiveUsername_s,User_s,EventSubclass_s,DurationMs
| order by StartTime_t asc
```

#### <a name="example-2"></a>範例 2

下列查詢會傳回伺服器的記憶體和 QPU 耗用量。 如果已向外延展，因為 ServerName_s 中包含複本編號，將會依複本來細分結果。

```Kusto
let window = AzureDiagnostics
   | where ResourceProvider == "MICROSOFT.ANALYSISSERVICES" and Resource =~ "MyServerName";
window
| where OperationName == "LogMetric" 
| where name_s == "memory_metric" or name_s == "qpu_metric"
| project ServerName_s, TimeGenerated, name_s, value_s
| summarize avg(todecimal(value_s)) by ServerName_s, name_s, bin(TimeGenerated, 1m)
| order by TimeGenerated asc 
```

#### <a name="example-3"></a>範例 3

下列查詢會傳回伺服器的 Rows read/sec Analysis Services 引擎效能計數器。

```Kusto
let window =  AzureDiagnostics
   | where ResourceProvider == "MICROSOFT.ANALYSISSERVICES" and Resource =~ "MyServerName";
window
| where OperationName == "LogMetric" 
| where parse_json(tostring(parse_json(perfobject_s).counters))[0].name == "Rows read/sec" 
| extend Value = tostring(parse_json(tostring(parse_json(perfobject_s).counters))[0].value) 
| project ServerName_s, TimeGenerated, Value
| summarize avg(todecimal(Value)) by ServerName_s, bin(TimeGenerated, 1m)
| order by TimeGenerated asc 
```

有數百個查詢可供使用。 若要深入了解查詢，請參閱[開始使用 Azure 監視器記錄查詢](../azure-monitor/log-query/get-started-queries.md)。


## <a name="turn-on-logging-by-using-powershell"></a>使用 PowerShell 開啟記錄

在本快速教學課程中，您可以在與 Analysis Service 伺服器相同的訂用帳戶和資源群組中，建立儲存體帳戶。 接著，您使用 Set-AzDiagnosticSetting 開啟診斷記錄，將輸出傳送至新的儲存體帳戶。

### <a name="prerequisites"></a>Prerequisites
若要完成本教學課程，您必須具備下列資源：

* 現有的 Azure Analysis Services 伺服器。 如需建立伺服器資源的指示，請參閱 [在 Azure 入口網站中建立伺服器](analysis-services-create-server.md)，或[使用 PowerShell 建立 Azure Analysis Services 伺服器](analysis-services-create-powershell.md)。

### <a name="aconnect-to-your-subscriptions"></a></a>連線到您的訂閱

開始 Azure PowerShell 工作階段，並使用下列命令登入您的 Azure 帳戶：  

```powershell
Connect-AzAccount
```

在快顯瀏覽器視窗中，輸入您的 Azure 帳戶使用者名稱與密碼。 Azure PowerShell 會取得與此帳戶相關聯的所有訂用帳戶，並依預設使用第一個訂用帳戶。

如果您有多個訂用帳戶，您可能必須指定用來建立 Azure 金鑰保存庫的那一個特定訂用帳戶。 輸入下列命令以查看您帳戶的訂用帳戶：

```powershell
Get-AzSubscription
```

然後為了指定已與所要記錄 Azure Analysis Services 帳戶建立關聯的訂用帳戶，輸入：

```powershell
Set-AzContext -SubscriptionId <subscription ID>
```

> [!NOTE]
> 如果您有多個與帳戶相關聯的訂用帳戶，請務必指定訂用帳戶。
>
>

### <a name="create-a-new-storage-account-for-your-logs"></a>為您的記錄建立新的儲存體帳戶

您可以針對您的記錄使用現有的儲存體帳戶，前提是該帳戶與您的伺服器位於相同的訂用帳戶中。 針對此教學課程，您會建立專門用於 Analysis Services 記錄的新儲存體帳戶。 為了方便起見，您可將儲存體帳戶詳細資訊儲存到名為 **sa** 的變數。

您也可以使用與包含 Analysis Services 伺服器資源群組相同的資源群組。 請將 `awsales_resgroup`、`awsaleslogs` 和 `West Central US` 的值替代成您自己的值：

```powershell
$sa = New-AzStorageAccount -ResourceGroupName awsales_resgroup `
-Name awsaleslogs -Type Standard_LRS -Location 'West Central US'
```

### <a name="identify-the-server-account-for-your-logs"></a>識別記錄的伺服器帳戶

將帳戶名稱設為名為 **account** 的變數，其中的 ResourceName 是帳戶的名稱。

```powershell
$account = Get-AzResource -ResourceGroupName awsales_resgroup `
-ResourceName awsales -ResourceType "Microsoft.AnalysisServices/servers"
```

### <a name="enable-logging"></a>啟用記錄

若要啟用記錄，請使用 Set-AzDiagnosticSetting Cmdlet，並搭配新儲存體帳戶、伺服器帳戶和類別的變數一起使用。 執行下列命令，將 **-Enabled** 旗標設為 **$true**：

```powershell
Set-AzDiagnosticSetting  -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories Engine
```

輸出應會如下列範例所示：

```powershell
StorageAccountId            : 
/subscriptions/a23279b5-xxxx-xxxx-xxxx-47b7c6d423ea/resourceGroups/awsales_resgroup/providers/Microsoft.Storage/storageAccounts/awsaleslogs
ServiceBusRuleId            :
EventHubAuthorizationRuleId :
Metrics                    
    TimeGrain       : PT1M
    Enabled         : False
    RetentionPolicy
    Enabled : False
    Days    : 0


Logs                       
    Category        : Engine
    Enabled         : True
    RetentionPolicy
    Enabled : False
    Days    : 0


    Category        : Service
    Enabled         : False
    RetentionPolicy
    Enabled : False
    Days    : 0


WorkspaceId                 :
Id                          : /subscriptions/a23279b5-xxxx-xxxx-xxxx-47b7c6d423ea/resourcegroups/awsales_resgroup/providers/microsoft.analysisservic
es/servers/awsales/providers/microsoft.insights/diagnosticSettings/service
Name                        : service
Type                        :
Location                    :
Tags                        :
```

這個輸出結果可確認伺服器記錄現已啟用，系統會將資訊儲存到儲存體帳戶中。

您也可以設定記錄的保留原則，以便自動刪除較舊的記錄。 例如，使用 **-RetentionEnabled** 旗標將保留原則設為 **$true** 並將 **-RetentionInDays** 參數設為 **90**。 系統將自動刪除超過 90 天的舊記錄。

```powershell
Set-AzDiagnosticSetting -ResourceId $account.ResourceId`
 -StorageAccountId $sa.Id -Enabled $true -Categories Engine`
  -RetentionEnabled $true -RetentionInDays 90
```

## <a name="next-steps"></a>後續步驟

深入了解 [Azure 監視器資源記錄](../azure-monitor/platform/platform-logs-overview.md)。

請參閱 PowerShell 說明中的 [Set-AzDiagnosticSetting](https://docs.microsoft.com/powershell/module/az.monitor/set-azdiagnosticsetting)。
