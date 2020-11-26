---
title: 使用邏輯應用程式將資料從 Log Analytics 工作區封存至 Azure 儲存體
description: 描述使用 Azure Logic Apps 從 Log Analytics 工作區查詢資料並傳送至 Azure 儲存體的方法。
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/02/2020
ms.openlocfilehash: 04f1eb0d9db00a2be1a4619cafe38aa18145fc78
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/26/2020
ms.locfileid: "96185992"
---
# <a name="archive-data-from-log-analytics-workspace-to-azure-storage-using-logic-app"></a>使用邏輯應用程式將資料從 Log Analytics 工作區封存至 Azure 儲存體
本文說明使用 [Azure Logic Apps](../../logic-apps/index.yml) 從 Azure 監視器中的 Log Analytics 工作區查詢資料並傳送至 Azure 儲存體的方法。 當您需要匯出 Azure 監視器記錄資料來進行審核和合規性案例，或允許其他服務取得此資料時，請使用此程式。  

## <a name="other-export-methods"></a>其他匯出方法
本文所述的方法描述使用邏輯應用程式從記錄查詢進行的排程匯出。 針對特定案例匯出資料的其他選項包括下列各項：

- 若要將 Log Analytics 工作區中的所有資料匯出至 Azure 儲存體帳戶或事件中樞，請使用 Azure 監視器記錄的 Log Analytics 工作區資料匯出功能。 請參閱 [Azure 監視器 (preview 中的 Log Analytics 工作區資料匯出) ](logs-data-export.md)
- 使用邏輯應用程式進行一次匯出。 請參閱 [Logic Apps 和 Power Automate 的 Azure 監視器記錄連接器](logicapp-flow-connector.md)。
- 使用 PowerShell 腳本一次匯出到本機電腦。 請參閱 [AzOperationalInsightsQueryExport]] (https://www.powershellgallery.com/packages/Invoke-AzOperationalInsightsQueryExport) 。

## <a name="overview"></a>總覽
此程式會使用 [Azure 監視器記錄連接器](/connectors/azuremonitorlogs/) ，可讓您從邏輯應用程式執行記錄查詢，並在工作流程中的其他動作中使用其輸出。 此程式會使用 [Azure Blob 儲存體連接器](/connectors/azureblob/) ，將查詢輸出傳送至 Azure 儲存體。 其他動作將在下列各節中說明。

![邏輯應用程式總覽](media/logs-export-logicapp/logic-app-overview.png)

當您從 Log Analytics 工作區匯出資料時，您應該篩選和匯總記錄資料，並將查詢優化，以將邏輯應用程式工作流程所處理的資料量限制為所需的資料。 例如，如果您需要封存登入事件，您可以使用下列查詢來篩選所需的事件，並只投影必要的欄位： 

```json
SecurityEvent
| where EventID == 4624 or EventID == 4625
| project TimeGenerated , Account , AccountType , Computer
```

當您依排程匯出資料時，請在查詢中使用 ingestion_time ( # A1 函式，以確保您不會錯過延遲抵達的資料。 如果資料因為網路或平臺問題而延遲，使用內嵌時間可確保它會包含在下一個邏輯應用程式執行中。 如需範例，請參閱 [新增 Azure 監視器記錄動作](#add-azure-monitor-logs-action) 。

## <a name="prerequisites"></a>Prerequisites
以下是完成此程式之前必須完成的必要條件。

- Log Analytics 工作區。 建立邏輯應用程式的使用者必須至少擁有工作區的讀取權限。 
- Azure 儲存體帳戶。 儲存體帳戶不一定要與您的 Log Analytics 工作區位於相同的訂用帳戶中。 建立邏輯應用程式的使用者必須具有儲存體帳戶的寫入權限。 


## <a name="connector-limits"></a>連接器限制
Azure 監視器中的 log Analytics 工作區和記錄查詢都是多租使用者服務，其中包含可保護及隔離客戶及維護服務品質的限制。 查詢大量資料時，您應該考慮下列限制，這可能會影響您設定邏輯應用程式迴圈與記錄查詢的方式：

- 記錄查詢無法傳回超過500000個數據列。
- 記錄查詢無法傳回超過64000000個位元組。
- 依預設，記錄查詢無法執行超過10分鐘。 
- Log Analytics 連接器的限制為每分鐘100個呼叫。


## <a name="create-container-in-the-storage-account"></a>在儲存體帳戶中建立容器
使用 [建立容器](../../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container) 中的程式，將容器新增至儲存體帳戶，以保存匯出的資料。 本文中用於容器的名稱是 **loganalytics 資料**，但您可以使用任何名稱。


## <a name="create-logic-app"></a>建立邏輯應用程式

移至 Azure 入口網站中的 **Logic Apps** ，然後按一下 [ **新增**]。 選取 **訂** 用帳戶、 **資源群組** 和 **區域** 以儲存新的邏輯應用程式，然後為其指定唯一的名稱。 您可以開啟 **Log Analytics** 設定，以收集有關執行時間資料和事件的資訊，如 [設定 Azure 監視器記錄和收集診斷資料以供 Azure Logic Apps](../../logic-apps/monitor-logic-apps-log-analytics.md)所述。 使用 Azure 監視器記錄連接器不需要此設定。

![建立邏輯應用程式](media/logs-export-logicapp/create-logic-app.png)


按一下 [ **審核 + 建立** ]，然後 **建立**。 當部署完成時，按一下 [ **移至資源** ] 以開啟 **Logic Apps 設計** 工具。

## <a name="create-a-trigger-for-the-logic-app"></a>建立邏輯應用程式的觸發程式
在 [ **開始使用一般觸發** 程式] 下，選取 **[週期**]。 這會建立可定期自動執行的邏輯應用程式。 在動作的 [ **頻率** ] 方塊中，選取 [ **小時** ]，然後在 [ **間隔** ] 方塊中輸入 **1** ，每天執行一次工作流程。

![週期動作](media/logs-export-logicapp/recurrence-action.png)


### <a name="add-azure-monitor-logs-action"></a>新增 Azure 監視器記錄檔動作
按一下 [ **+ 新增步驟** ]，加入在迴圈動作之後執行的動作。 在 **[選擇動作**] 下，輸入 **azure 監視器** ，然後選取 [ **Azure 監視器記錄**]。

![Azure 監視器記錄檔動作](media/logs-export-logicapp/select-azure-monitor-connector.png)

按一下 [ **Azure Log Analytics –執行查詢並列出結果**]。

![在邏輯應用程式設計工具的步驟中新增動作的螢幕擷取畫面。 在 [選擇動作] 下，會反白顯示 Azure 監視器記錄。](media/logs-export-logicapp/select-query-action-list.png)

系統會提示您選取租使用者，並使用工作流程將用來執行查詢的帳戶來授與 Log Analytics 工作區的存取權。


## <a name="add-azure-monitor-logs-action"></a>新增 Azure 監視器記錄檔動作
Azure 監視器 Logs] 動作可讓您指定要執行的查詢。 此範例中使用的記錄查詢已針對每小時週期優化，並會收集特定執行時間的資料內嵌。 例如，如果工作流程在4:35 執行，時間範圍會是4:00 到5:00。 如果您將邏輯應用程式變更為以不同的頻率執行，您也需要變更查詢。 例如，如果您將迴圈設定為每天執行，您會在查詢中設定 startTime 以 startofday (make_datetime (年、月、日、0、0) # A3。 

選取 Log Analytics 工作區的 **訂** 用帳戶和 **資源群組** 。 選取 **資源類型** 的 *Log Analytics 工作區*，然後在 [**資源名稱**] 底下選取工作區的名稱。

在 [查詢] 視窗新增下列記錄查詢。  

```Kusto
let dt = now();
let year = datetime_part('year', dt);
let month = datetime_part('month', dt);
let day = datetime_part('day', dt);
let hour = datetime_part('hour', dt);
let startTime = make_datetime(year,month,day,hour,0)-1h;
let endTime = startTime + 1h - 1tick;
AzureActivity
| where ingestion_time() between(startTime .. endTime)
| project 
    TimeGenerated,
    BlobTime = startTime, 
    OperationName ,
    OperationNameValue ,
    Level ,
    ActivityStatus ,
    ResourceGroup ,
    SubscriptionId ,
    Category ,
    EventSubmissionTimestamp ,
    ClientIpAddress = parse_json(HTTPRequest).clientIpAddress ,
    ResourceId = _ResourceId 
```

**時間範圍** 會根據 **TimeGenerated** 資料行指定要包含在查詢中的記錄。 這應該設定為等於或大於查詢中所選時間範圍的值。 因為此查詢不使用 **TimeGenerated** 資料行，所以無法使用 [ **在查詢中設定** ] 選項。 如需時間範圍的詳細資訊，請參閱 [查詢範圍](../log-query/scope.md) 。 

選取 [ **過去4小時** ] 作為 **時間範圍**。 這可確保結果中會包含任何內嵌時間大於 **TimeGenerated** 的記錄。
   
![新 Azure 監視器記錄檔的設定的螢幕擷取畫面，名為 [執行查詢]，並以視覺化方式呈現結果。](media/logs-export-logicapp/run-query-list-action.png)


### <a name="add-parse-json-activity-optional"></a>將剖析 JSON 活動新增 (選擇性) 
[ **執行查詢] 和 [清單結果** ] 動作的輸出會以 JSON 格式格式化。 您可以剖析此資料，並在準備 **撰寫** 動作時操作它。 

您可以提供 JSON 架構來描述您預期接收的承載。 此設計工具會使用此結構描述來剖析 JSON 內容，並產生方便使用的權杖來表示 JSON 內容中的屬性。 您可以在邏輯應用程式的整個工作流程中輕鬆地參考和使用這些屬性。 


按一下 [ **+ 新增步驟**]，然後按一下 [ **+ 新增動作**]。 在 **[選擇動作**] 下，輸入 **json** ，然後選取 [ **剖析 json**]。

![選取剖析 JSON 活動](media/logs-export-logicapp/select-parse-json.png)

按一下 [ **內容** ] 方塊，以顯示先前活動中的值清單。 從 [**執行查詢] 和 [清單結果**] 動作中選取 [**主體**]。 這是記錄查詢的輸出。

[![選取主體](media/logs-export-logicapp/select-body.png)](media/logs-export-logicapp/select-body.png#lightbox)

5.  按一下 [ **使用範例承載產生架構**]。 執行記錄查詢並複製輸出，以用於範例承載。  在這裡的範例查詢中，您可以使用下列輸出：


```json
{
    "TimeGenerated": "2020-09-29T23:11:02.578Z",
    "BlobTime": "2020-09-29T23:00:00Z",
    "OperationName": "Returns Storage Account SAS Token",
    "OperationNameValue": "MICROSOFT.RESOURCES/DEPLOYMENTS/WRITE",
    "Level": "Informational",
    "ActivityStatus": "Started",
    "ResourceGroup": "monitoring",
    "SubscriptionId": "00000000-0000-0000-0000-000000000000",
    "Category": "Administrative",
    "EventSubmissionTimestamp": "2020-09-29T23:11:02Z",
    "ClientIpAddress": "192.168.1.100",
    "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/monitoring/providers/microsoft.storage/storageaccounts/my-storage-account"
}
```



![剖析 JSON 承載](media/logs-export-logicapp/parse-json-payload.png)

## <a name="add-the-compose-action"></a>新增撰寫動作
**撰寫** 動作會採用剖析的 JSON 輸出，並建立您需要儲存在 blob 中的物件。

按一下 [ **+ 新增步驟**]，然後按一下 [ **+ 新增動作**]。 在 **[選擇動作**] 下，輸入 [ **撰寫** ]，然後選取 [ **撰寫** ] 動作。

![選取撰寫動作](media/logs-export-logicapp/select-compose.png)


按一下 [ **輸入** ] 方塊，即可顯示先前活動中的值清單。 選取 [**剖析 JSON** ] 動作中的 [**主體**]。 這是記錄查詢中已剖析的輸出。

[![選取撰寫動作的主體](media/logs-export-logicapp/select-body-compose.png)](media/logs-export-logicapp/select-body-compose.png#lightbox)


## <a name="add-the-create-blob-action"></a>新增建立 Blob 動作
[建立 Blob] 動作會將組成的 JSON 寫入至儲存體。

按一下 [ **+ 新增步驟**]，然後按一下 [ **+ 新增動作**]。 在 **[選擇動作**] 下，輸入 **blob** ，然後選取 [ **建立 blob** ] 動作。

![選取建立 blob](media/logs-export-logicapp/select-create-blob.png)

在 [連線 **名稱** ] 中輸入儲存體帳戶連線的名稱，然後按一下 [ **資料夾路徑** ] 方塊中的資料夾圖示，以選取儲存體帳戶中的容器。 按一下 **Blob 名稱** 以查看先前活動中的值清單。 按一下 [ **運算式** ]，並輸入符合您時間間隔的運算式。 針對每小時執行的此查詢，下列運算式會設定每個前一小時的 blob 名稱： 

```json
subtractFromTime(formatDateTime(utcNow(),'yyyy-MM-ddTHH:00:00'), 1,'Hour')
```

[![Blob 運算式](media/logs-export-logicapp/blob-expression.png)](media/logs-export-logicapp/blob-expression.png#lightbox)

按一下 [ **Blob 內容**] 方塊，以顯示先前活動中的值清單，然後在 [**撰寫** 中] 區段中選取 [**輸出**]。


![建立 blob 運算式](media/logs-export-logicapp/create-blob.png)


## <a name="test-the-logic-app"></a>測試邏輯應用程式
按一下 [ **執行**] 來測試工作流程。 如果工作流程有錯誤，則會在發生問題的步驟中指出。 您可以查看執行，並向下切入每個步驟來查看輸入和輸出，以調查失敗。 如有必要，請參閱 [疑難排解和診斷 Azure Logic Apps 中的工作流程失敗](../../logic-apps/logic-apps-diagnosing-failures.md) 。

[![執行歷程記錄](media/logs-export-logicapp/runs-history.png)](media/logs-export-logicapp/runs-history.png#lightbox)


## <a name="view-logs-in-storage"></a>查看儲存體中的記錄
移至 Azure 入口網站中的 [ **儲存體帳戶** ] 功能表，然後選取您的儲存體帳戶。 按一下 [ **blob** ] 磚，然後選取您在 [建立 blob] 動作中指定的容器。 選取其中一個 blob，然後 **編輯 blob**。

[![Blob 資料](media/logs-export-logicapp/blob-data.png)](media/logs-export-logicapp/blob-data.png#lightbox)

## <a name="next-steps"></a>後續步驟

- 深入了解 [Azure 監視器中的記錄查詢](../log-query/log-query-overview.md)。
- 深入瞭解 [Logic Apps](../../logic-apps/index.yml)
- 深入瞭解 [Power Automate](https://flow.microsoft.com)。