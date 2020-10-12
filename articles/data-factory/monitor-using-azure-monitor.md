---
title: 使用 Azure 監視器來監視 Data Factory
description: 瞭解如何使用 Azure 監視器，以 Data Factory 中的資訊啟用診斷記錄，以監視/Azure Data Factory 管線。
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 07/13/2020
ms.openlocfilehash: 6f0e688f3d483536e0d82186dd8e498cdadf97da
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87563546"
---
# <a name="monitor-and-alert-data-factory-by-using-azure-monitor"></a>使用 Azure 監視器監視和警示 Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

雲端應用程式很複雜，而且有許多移動元件。 監視器會提供資料，以協助確保您的應用程式保持正常運作狀態。 監視也可協助您避免發生潛在問題，並針對過去的問題進行疑難排解。 您可以使用監視資料來深入瞭解您的應用程式。 這種知識可協助您改善應用程式效能和可維護性。 它也可協助您將需要手動介入的動作自動化。

Azure 監視器針對大部分的 Azure 服務提供基本層級的基礎結構計量和記錄。 Azure 診斷記錄是由資源發出，並提供關於該資源作業的豐富、經常性資料。 Azure Data Factory (ADF) 可以在 Azure 監視器中寫入診斷記錄。 如需此功能的 7 分鐘簡介與示範，請觀看下列影片：

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Monitor-Data-Factory-pipelines-using-Operations-Management-Suite-OMS/player]

如需詳細資訊，請參閱 [Azure 監視器概觀](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor)。

## <a name="keeping-azure-data-factory-metrics-and-pipeline-run-data"></a>保留 Azure Data Factory 計量和管線執行資料

Data Factory 只會在45天內儲存管線執行資料。 如果您想要將資料保留較長的時間，請使用 Azure 監視器。 使用監視器，您可以將診斷記錄路由傳送至多個不同的目標。

* **儲存體帳戶**：將診斷記錄儲存到儲存體帳戶，以供進行審核或手動檢查。 您可以使用診斷設定來指定保留時間（以天為單位）。
* **事件中樞**：將記錄串流至 Azure 事件中樞。 記錄會變成夥伴服務/自訂分析解決方案（例如 Power BI）的輸入。
* **Log analytics**：使用 log analytics 分析記錄。 Data Factory 與 Azure 監視器的整合在下列案例中很有用：
  * 您想要針對由 Data Factory 所發行的一組豐富的計量，撰寫複雜的查詢來進行監視。 您可以透過「監視」來建立這些查詢的自訂警示。
  * 您想要監視所有資料處理站。 您可以將多個資料處理站的資料路由傳送至單一監視器工作區。

您也可以使用不在發出記錄的資源訂用帳戶中的儲存體帳戶或事件中樞命名空間。 設定此設定的使用者必須具有適當的角色型存取控制 (RBAC) 存取這兩個訂用帳戶。

## <a name="configure-diagnostic-settings-and-workspace"></a>設定診斷設定和工作區

為您的 data factory 建立或新增診斷設定。

1. 在入口網站中，移至 [監視]。 選取 [**設定**  >  **診斷設定**]。

1. 選取您要設定診斷設定的 data factory。

1. 如果選取的資料處理站上沒有設定存在，系統會提示您建立設定。 選取 [開啟診斷]。

   ![如果沒有設定存在，請建立診斷設定](media/data-factory-monitor-oms/monitor-oms-image1.png)

   如果資料處理站上有現有的設定，您會看到已在 data factory 上設定的設定清單。 選取 [ **新增診斷設定**]。

   ![如果設定存在，請新增診斷設定](media/data-factory-monitor-oms/add-diagnostic-setting.png)

1. 為您的設定提供名稱，選取 [ **傳送至 Log analytics**]，然後從 **Log analytics 工作區**選取工作區。

    * 在 _Azure 診斷_ 模式中，診斷記錄會流入 _AzureDiagnostics_ 資料表。

    * 在 _資源特定_ 模式中，從 Azure Data Factory 流到下表的診斷記錄：
      - _ADFActivityRun_
      - _ADFPipelineRun_
      - _ADFTriggerRun_
      - _ADFSSISIntegrationRuntimeLogs_
      - _ADFSSISPackageEventMessageCoNtext_
      - _ADFSSISPackageEventMessages_
      - _ADFSSISPackageExecutableStatistics_
      - _ADFSSISPackageExecutionComponentPhases_
      - _ADFSSISPackageExecutionDataStatistics_

      您可以選取與您的工作負載相關的各種記錄，以傳送至 Log Analytics 資料表。 例如，如果您未使用 SQL Server Integration Services (SSIS) ，則不需要選取任何 SSIS 記錄。 如果您想要記錄 SSIS Integration Runtime (IR) 啟動/停止/維護作業，您可以選取 SSIS IR 記錄檔。 如果您透過 T-sql 在 SQL Server Management Studio 上叫用 SSIS 套件執行 (SSMS) 、SQL Server Agent 或其他指定的工具，您可以選取 [SSIS 套件記錄檔]。 如果您透過 ADF 管線中的執行 SSIS 套件活動來叫用 SSIS 套件執行，您可以選取所有記錄。

    * 如果您選取 [ _AllMetrics_]，將會提供各種 ADF 計量讓您監視或引發警示，包括 ADF 活動、管線和觸發程式執行的計量，以及 ssis IR 作業和 ssis 套件執行。

   ![命名您的設定，然後選取記錄分析工作區](media/data-factory-monitor-oms/monitor-oms-image2.png)

    > [!NOTE]
    > 因為 Azure 記錄資料表不能有超過500個數據行，所以 **強烈建議** 您選取 _資源特定模式_。 如需詳細資訊，請參閱 [Log Analytics 的已知限制](../azure-monitor/platform/resource-logs-collect-workspace.md#column-limit-in-azurediagnostics)。

1. 選取 [儲存]****。

經過幾分鐘之後，新的設定就會出現在此 data factory 的設定清單中。 一旦產生新的事件資料，就會將診斷記錄串流處理至該工作區。 當事件發出和記錄在 Log Analytics 中出現時，最多可能會有15分鐘的時間。

## <a name="install-azure-data-factory-analytics-solution-from-azure-marketplace"></a>從 Azure Marketplace 安裝 Azure Data Factory 分析解決方案

此解決方案提供您 Data Factory 整體健康情況的摘要，並提供可深入瞭解詳細資料的選項，並針對非預期的行為模式進行疑難排解。 有了豐富的現成觀點，您可以取得重要處理的見解，包括：

* 查看 data factory 管線、活動和觸發程式執行的一覽摘要
* 能夠依類型切入至 data factory 活動執行
* Data factory 最上層管線、活動錯誤的摘要

1. 移至 **Azure Marketplace**，選擇 [ **分析** 篩選]，並搜尋 **Azure Data Factory 分析 (預覽) **

   ![移至 [Azure Marketplace]，輸入「分析篩選」，然後選取「Azure Data Factory 分析 (預覽」 ) ](media/data-factory-monitor-oms/monitor-oms-image3.png)

1. **Azure Data Factory 分析 (預覽**的詳細資料) 

   ![「Azure Data Factory 分析 (預覽) 」的詳細資料](media/data-factory-monitor-oms/monitor-oms-image4.png)

1. 選取 [ **建立** ]，然後建立或選取 **Log Analytics 工作區**。

   ![建立新的解決方案](media/data-factory-monitor-oms/monitor-log-analytics-image-5.png)

### <a name="monitor-data-factory-metrics"></a>監視 Data Factory 計量

安裝此解決方案會在所選 Log Analytics 工作區的活頁簿區段內建立一組預設的視圖。 如此一來，將會啟用下列計量：

* ADF 執行-1) 管線執行 Data Factory
* ADF 執行-2) 活動執行 Data Factory
* ADF 執行-3) 觸發程式執行 Data Factory
* ADF 錯誤-1) 前10個管線錯誤，Data Factory
* ADF 錯誤-2) 前10個活動執行 Data Factory
* ADF 錯誤-3) 前10個觸發程式錯誤，Data Factory
* ADF 統計資料-1) 活動執行（依類型）
* ADF 統計資料-2) 觸發程式執行（依類型）
* ADF 統計資料-3) 管線執行持續期間上限

![已反白顯示 [活頁簿 (預覽) ] 和 [AzureDataFactoryAnalytics] 的視窗](media/data-factory-monitor-oms/monitor-oms-image6.png)

您可以將上述的計量視覺化、查看這些計量背後的查詢、編輯查詢、建立警示，以及採取其他動作。

![由 data factory 以圖形方式呈現管線執行」](media/data-factory-monitor-oms/monitor-oms-image8.png)

> [!NOTE]
> Azure Data Factory Analytics (Preview) 將診斷記錄傳送到 _資源特定的_ 目的地資料表。 您可以針對下列資料表撰寫查詢： _ADFPipelineRun_、 _ADFTriggerRun_和 _ADFActivityRun_。

## <a name="data-factory-metrics"></a>Data Factory 計量

使用監視器，您可以看到 Azure 工作負載的效能和健康情況。 最重要的監視資料類型是度量，也稱為效能計數器。 計量是由大多數 Azure 資源所發出。 監視器提供數種方式來設定和使用這些計量來進行監視和疑難排解。

以下是 Azure Data Factory 第2版發出的一些計量：

| **計量**                           | **度量顯示名稱**                  | **單位** | **彙總類型** | **說明**                |
|--------------------------------------|------------------------------------------|----------|----------------------|--------------------------------|
| ActivityCancelledRuns                 | 已取消的活動執行計量           | Count    | 總計                | 分鐘時間範圍內已取消的活動執行總數。 |
| ActivityFailedRuns                   | 失敗的活動執行計量             | Count    | 總計                | 在分鐘時間範圍內失敗的活動執行總數。 |
| ActivitySucceededRuns                | 成功的活動執行計量          | Count    | 總計                | 在分鐘時間範圍內成功的活動執行總數。 |
| PipelineCancelledRuns                 | 已取消的管線執行計量           | Count    | 總計                | 分鐘時間範圍內已取消的管線執行總數。 |
| PipelineFailedRuns                   | 失敗的管線執行計量             | Count    | 總計                | 在分鐘時間範圍內失敗的管線執行總數。 |
| PipelineSucceededRuns                | 成功的管線執行計量          | Count    | 總計                | 在分鐘時間範圍內成功執行的管線執行總數。 |
| TriggerCancelledRuns                  | 已取消的觸發程序執行計量            | Count    | 總計                | 在分鐘時間範圍內取消的觸發程式執行總數。 |
| TriggerFailedRuns                    | 失敗的觸發程序執行計量              | Count    | 總計                | 在分鐘時間範圍內失敗的觸發程式執行總數。 |
| TriggerSucceededRuns                 | 成功的觸發程序執行計量           | Count    | 總計                | 在分鐘時間範圍內成功執行的觸發程式執行總數。 |
| SSISIntegrationRuntimeStartCancelled  | 已取消 SSIS integration runtime 開始計量           | Count    | 總計                | 在一分鐘時間範圍內已取消的 SSIS integration runtime 啟動總數。 |
| SSISIntegrationRuntimeStartFailed    | 失敗的 SSIS 整合執行時間啟動計量             | Count    | 總計                | 在一分鐘的時間範圍內，SSIS 整合執行時間的啟動失敗總數。 |
| SSISIntegrationRuntimeStartSucceeded | 成功的 SSIS 整合執行時間開始計量          | Count    | 總計                | 在一分鐘時間範圍內成功啟動的 SSIS integration runtime 總數。 |
| SSISIntegrationRuntimeStopStuck      | 停滯 SSIS integration runtime 停止計量               | Count    | 總計                | 在一分鐘時間範圍內停滯的 SSIS integration runtime 停止總數。 |
| SSISIntegrationRuntimeStopSucceeded  | 成功的 SSIS 整合執行時間停止計量           | Count    | 總計                | 在一分鐘內成功的 SSIS integration runtime 停止總數。 |
| SSISPackageExecutionCancelled         | 已取消 SSIS 套件執行計量  | Count    | 總計                | 在一分鐘時間範圍內取消的 SSIS 封裝執行總數。 |
| SSISPackageExecutionFailed           | 失敗的 SSIS 套件執行計量    | Count    | 總計                | 在一分鐘時間範圍內失敗的 SSIS 封裝執行總數。 |
| SSISPackageExecutionSucceeded        | 成功的 SSIS 套件執行計量 | Count    | 總計                | 在分鐘時間範圍內成功執行的 SSIS 封裝執行總數。 |

若要存取度量，請完成 [Azure 監視器資料平臺](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics)中的指示。

> [!NOTE]
> 只會發出已完成、觸發的活動和管線執行的事件。 進行中且 **不** 會發出 debug 執行。 另一方面， **所有** SSIS 封裝執行的事件都會發出，包括已完成和進行中的事件，不論其調用方法為何。 例如，您可以透過 SSMS 上的 T-sql、SQL Server Agent 或其他指定的工具，在啟用 Azure 的 SQL Server Data Tools (SSDT) 上叫用封裝，以及在 ADF 管線中，將執行 SSIS 套件活動的觸發或偵測執行視為已觸發或執行偵測。

## <a name="data-factory-alerts"></a>Data Factory 警示

登入 Azure 入口網站，然後選取 [**監視**  >  **警示**] 以建立警示。

![入口網站功能表中的警示](media/monitor-using-azure-monitor/alerts_image3.png)

### <a name="create-alerts"></a>建立警示

1. 選取 [+ 新增警示規則]**** 來新建警示。

    ![新增警示規則](media/monitor-using-azure-monitor/alerts_image4.png)

1. 定義警示條件。

    > [!NOTE]
    > 請務必在 [**依資源類型篩選**] 下拉式清單中選取 [**全部**]。

    ![「定義警示條件」 > 「選取目標」，這會開啟 [選取資源] 窗格 ](media/monitor-using-azure-monitor/alerts_image5.png)

    ![「定義警示條件」 > [新增準則]，這會開啟 [設定信號邏輯] 窗格](media/monitor-using-azure-monitor/alerts_image6.png)

    ![[設定信號類型] 窗格](media/monitor-using-azure-monitor/alerts_image7.png)

1. 定義警示詳細資料。

    ![警示詳細資料](media/monitor-using-azure-monitor/alerts_image8.png)

1. 定義動作群組。

    ![建立規則，並反白顯示 [新增動作群組]](media/monitor-using-azure-monitor/alerts_image9.png)

    ![建立新的動作群組](media/monitor-using-azure-monitor/alerts_image10.png)

    ![設定電子郵件、SMS、推播和語音](media/monitor-using-azure-monitor/alerts_image11.png)

    ![定義動作群組](media/monitor-using-azure-monitor/alerts_image12.png)

## <a name="set-up-diagnostic-logs-via-the-azure-monitor-rest-api"></a>透過 Azure 監視器 REST API 設定診斷記錄

### <a name="diagnostic-settings"></a>診斷設定

使用診斷設定來設定非計算資源的診斷記錄。 資源控制項的設定具有下列功能：

* 它們指定診斷記錄的傳送位置。 範例包括 Azure 儲存體帳戶、Azure 事件中樞或監視器記錄。
* 他們會指定要傳送的記錄類別。
* 他們指定每個記錄類別應保留在儲存體帳戶中的時間長度。
* 保留期為 0 天表示會永遠保留記錄。 否則，此值可以是介於1到2147483647之間的任意天數。
* 如果已設定保留原則，但已停用儲存體帳戶中的記錄，保留原則不會有任何作用。 例如，只選取事件中樞或監視器記錄選項時，才會發生這種情況。
* 保留原則是每天套用的。 日期之間的界限會在午夜國際標準時間 (UTC) 。 在一天結束時，會刪除超過保留原則的天數內的記錄。 例如，如果您的保留原則為一天，在今天的開頭，就會刪除昨天之前的記錄。

### <a name="enable-diagnostic-logs-via-the-azure-monitor-rest-api"></a>透過 Azure 監視器 REST API 啟用診斷記錄

#### <a name="create-or-update-a-diagnostics-setting-in-the-monitor-rest-api"></a>在監視 REST API 中建立或更新診斷設定

##### <a name="request"></a>要求

```
PUT
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

##### <a name="headers"></a>headers

* 將 `{api-version}` 取代為 `2016-09-01`。
* 取代為 `{resource-id}` 您要編輯診斷設定之資源的識別碼。 如需詳細資訊，請參閱 [使用資源群組來管理您的 Azure 資源](../azure-resource-manager/management/manage-resource-groups-portal.md)。
* 將 `Content-Type` 標頭設定為 `application/json`。
* 將授權標頭設定為您從 Azure Active Directory (Azure AD) 取得的 JSON web 權杖。 如需詳細資訊，請參閱 [驗證要求](../active-directory/develop/authentication-scenarios.md)。

##### <a name="body"></a>主體

```json
{
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Storage/storageAccounts/<storageAccountName>",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.EventHub/namespaces/<eventHubName>/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<LogAnalyticsName>",
        "metrics": [
        ],
        "logs": [
                {
                    "category": "PipelineRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                },
                {
                    "category": "TriggerRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                },
                {
                    "category": "ActivityRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                }
            ]
    },
    "location": ""
}
```

| 屬性 | 類型 | 描述 |
| --- | --- | --- |
| **storageAccountId** |字串 | 您要將診斷記錄傳送至其中之儲存體帳戶的資源識別碼。 |
| **serviceBusRuleId** |字串 | 服務匯流排命名空間的服務匯流排規則識別碼，您想要在此命名空間中建立事件中樞以進行串流診斷記錄。 規則識別碼的格式為 `{service bus resource ID}/authorizationrules/{key name}` 。|
| **workspaceId** | 複雜類型 | 度量時間粒紋的陣列及其保留原則。 這個屬性的值是空的。 |
|**指標**| 要傳遞給已叫用之管線的管線執行參數值| 將參數名稱對應到引數值的 JSON 物件。 |
| **日誌**| 複雜類型| 資源類型之診斷記錄類別的名稱。 若要取得資源的診斷記錄類別清單，請執行「取得診斷設定」作業。 |
| **類別**| 字串| 記錄類別的陣列及其保留原則。 |
| **timeGrain** | 字串 | 計量的細微性，以 ISO 8601 持續時間格式來捕捉。 屬性值必須是 `PT1M` ，指定一分鐘。 |
| **啟用**| 布林值 | 指定是否為此資源啟用度量或記錄類別的收集。 |
| **retentionPolicy**| 複雜類型| 描述計量或記錄類別的保留原則。 這個屬性只會用於儲存體帳戶。 |
|**天**| Int| 要保留計量或記錄的天數。 如果屬性值為0，則會永遠保留記錄。 這個屬性只會用於儲存體帳戶。 |

##### <a name="response"></a>回應

200 確定。

```json
{
    "id": "/subscriptions/<subID>/resourcegroups/adf/providers/microsoft.datafactory/factories/shloadobetest2/providers/microsoft.insights/diagnosticSettings/service",
    "type": null,
    "name": "service",
    "location": null,
    "kind": null,
    "tags": null,
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.Storage/storageAccounts/<storageAccountName>",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.EventHub/namespaces/<eventHubName>/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.OperationalInsights/workspaces/<LogAnalyticsName>",
        "eventHubAuthorizationRuleId": null,
        "eventHubName": null,
        "metrics": [],
        "logs": [
            {
                "category": "PipelineRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "TriggerRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "ActivityRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            }
        ]
    },
    "identity": null
}
```

#### <a name="get-information-about-diagnostics-settings-in-the-monitor-rest-api"></a>取得監視器中診斷設定的相關資訊 REST API

##### <a name="request"></a>要求

```
GET
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

##### <a name="headers"></a>headers

* 將 `{api-version}` 取代為 `2016-09-01`。
* 取代為 `{resource-id}` 您要編輯診斷設定之資源的識別碼。 如需詳細資訊，請參閱 [使用資源群組來管理您的 Azure 資源](../azure-resource-manager/management/manage-resource-groups-portal.md)。
* 將 `Content-Type` 標頭設定為 `application/json`。
* 將授權標頭設定為您從 Azure AD 取得的 JSON web 權杖。 如需詳細資訊，請參閱 [驗證要求](../active-directory/develop/authentication-scenarios.md)。

##### <a name="response"></a>回應

200 確定。

```json
{
    "id": "/subscriptions/<subID>/resourcegroups/adf/providers/microsoft.datafactory/factories/shloadobetest2/providers/microsoft.insights/diagnosticSettings/service",
    "type": null,
    "name": "service",
    "location": null,
    "kind": null,
    "tags": null,
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/shloprivate/providers/Microsoft.Storage/storageAccounts/azmonlogs",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/shloprivate/providers/Microsoft.EventHub/namespaces/shloeventhub/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/ADF/providers/Microsoft.OperationalInsights/workspaces/mihaipie",
        "eventHubAuthorizationRuleId": null,
        "eventHubName": null,
        "metrics": [],
        "logs": [
            {
                "category": "PipelineRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "TriggerRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "ActivityRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            }
        ]
    },
    "identity": null
}
```
如需詳細資訊，請參閱 [診斷設定](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings)。

## <a name="schema-of-logs-and-events"></a>記錄和事件的架構

### <a name="monitor-schema"></a>監視架構

#### <a name="activity-run-log-attributes"></a>活動-執行記錄屬性

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "activityRunId":"",
   "pipelineRunId":"",
   "resourceId":"",
   "category":"ActivityRuns",
   "level":"Informational",
   "operationName":"",
   "pipelineName":"",
   "activityName":"",
   "start":"",
   "end":"",
   "properties":
       {
          "Input": "{
              "source": {
                "type": "BlobSource"
              },
              "sink": {
                "type": "BlobSink"
              }
           }",
          "Output": "{"dataRead":121,"dataWritten":121,"copyDuration":5,
               "throughput":0.0236328132,"errors":[]}",
          "Error": "{
              "errorCode": "null",
              "message": "null",
              "failureType": "null",
              "target": "CopyBlobtoBlob"
        }
    }
}
```

| 屬性 | 類型 | 描述 | 範例 |
| --- | --- | --- | --- |
| **Level** |字串 | 診斷記錄的層級。 針對活動執行記錄，將屬性值設定為4。 | `4` |
| **correlationId** |字串 | 用來追蹤特定要求的唯一識別碼。 | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **time** | 字串 | 事件的時間，以 timespan UTC 格式為限 `YYYY-MM-DDTHH:MM:SS.00000Z` 。 | `2017-06-28T21:00:27.3534352Z` |
|**activityRunId**| 字串| 活動執行的識別碼。 | `3a171e1f-b36e-4b80-8a54-5625394f4354` |
|**pipelineRunId**| 字串| 管線執行的識別碼。 | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|**resourceId**| 字串 | 與 data factory 資源相關聯的識別碼。 | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**類別**| 字串 | 診斷記錄的類別。 將屬性 (Property) 值設定為 `ActivityRuns`。 | `ActivityRuns` |
|**level**| 字串 | 診斷記錄的層級。 將屬性 (Property) 值設定為 `Informational`。 | `Informational` |
|**operationName**| 字串 | 活動的名稱及其狀態。 如果活動是啟動的信號，則屬性值為 `MyActivity -` 。 如果活動是結束信號，則屬性值為 `MyActivity - Succeeded` 。 | `MyActivity - Succeeded` |
|**pipelineName**| 字串 | 管線名稱。 | `MyPipeline` |
|**activityName**| 字串 | 活動名稱。 | `MyActivity` |
|**開始**| 字串 | 活動的開始時間以 timespan UTC 格式執行。 | `2017-06-26T20:55:29.5007959Z`|
|**end**| 字串 | 活動的結束時間以 timespan UTC 格式執行。 如果診斷記錄顯示活動已啟動但尚未結束，則屬性值為 `1601-01-01T00:00:00Z` 。 | `2017-06-26T20:55:29.5007959Z` |

#### <a name="pipeline-run-log-attributes"></a>管線執行記錄屬性

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "runId":"",
   "resourceId":"",
   "category":"PipelineRuns",
   "level":"Informational",
   "operationName":"",
   "pipelineName":"",
   "start":"",
   "end":"",
   "status":"",
   "properties":
    {
      "Parameters": {
        "<parameter1Name>": "<parameter1Value>"
      },
      "SystemParameters": {
        "ExecutionStart": "",
        "TriggerId": "",
        "SubscriptionId": ""
      }
    }
}
```

| 屬性 | 類型 | 描述 | 範例 |
| --- | --- | --- | --- |
| **Level** |字串 | 診斷記錄的層級。 針對活動執行記錄，將屬性值設定為4。 | `4` |
| **correlationId** |字串 | 用來追蹤特定要求的唯一識別碼。 | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **time** | 字串 | 事件的時間，以 timespan UTC 格式為限 `YYYY-MM-DDTHH:MM:SS.00000Z` 。 | `2017-06-28T21:00:27.3534352Z` |
|**runId**| 字串| 管線執行的識別碼。 | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|**resourceId**| 字串 | 與 data factory 資源相關聯的識別碼。 | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**類別**| 字串 | 診斷記錄的類別。 將屬性 (Property) 值設定為 `PipelineRuns`。 | `PipelineRuns` |
|**level**| 字串 | 診斷記錄的層級。 將屬性 (Property) 值設定為 `Informational`。 | `Informational` |
|**operationName**| 字串 | 管線的名稱及其狀態。 管線執行完成之後，屬性值為 `Pipeline - Succeeded` 。 | `MyPipeline - Succeeded`. |
|**pipelineName**| 字串 | 管線名稱。 | `MyPipeline` |
|**開始**| 字串 | 活動的開始時間以 timespan UTC 格式執行。 | `2017-06-26T20:55:29.5007959Z`. |
|**end**| 字串 | 活動的結束時間以 timespan UTC 格式執行。 如果診斷記錄顯示活動已啟動但尚未結束，則屬性值為 `1601-01-01T00:00:00Z` 。  | `2017-06-26T20:55:29.5007959Z` |
|**status**| 字串 | 管線執行的最終狀態。 可能的屬性值為 `Succeeded` 和 `Failed` 。 | `Succeeded`|

#### <a name="trigger-run-log-attributes"></a>觸發程式-執行記錄屬性

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "triggerId":"",
   "resourceId":"",
   "category":"TriggerRuns",
   "level":"Informational",
   "operationName":"",
   "triggerName":"",
   "triggerType":"",
   "triggerEvent":"",
   "start":"",
   "status":"",
   "properties":
   {
      "Parameters": {
        "TriggerTime": "",
       "ScheduleTime": ""
      },
      "SystemParameters": {}
    }
}
```

| 屬性 | 類型 | 描述 | 範例 |
| --- | --- | --- | --- |
| **Level** |字串 | 診斷記錄的層級。 針對活動執行記錄，將屬性值設定為4。 | `4` |
| **correlationId** |字串 | 用來追蹤特定要求的唯一識別碼。 | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **time** | 字串 | 事件的時間，以 timespan UTC 格式為限 `YYYY-MM-DDTHH:MM:SS.00000Z` 。 | `2017-06-28T21:00:27.3534352Z` |
|**triggerId**| 字串| 觸發程式執行的識別碼。 | `08587023010602533858661257311` |
|**resourceId**| 字串 | 與 data factory 資源相關聯的識別碼。 | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**類別**| 字串 | 診斷記錄的類別。 將屬性 (Property) 值設定為 `PipelineRuns`。 | `PipelineRuns` |
|**level**| 字串 | 診斷記錄的層級。 將屬性 (Property) 值設定為 `Informational`。 | `Informational` |
|**operationName**| 字串 | 觸發程式的名稱及其最終狀態，指出是否已成功引發觸發程式。 如果信號成功，則屬性值為 `MyTrigger - Succeeded` 。 | `MyTrigger - Succeeded` |
|**triggerName**| 字串 | 觸發程序的名稱。 | `MyTrigger` |
|**triggerType**| 字串 | 觸發程序的類型。 可能的屬性值為 `Manual Trigger` 和 `Schedule Trigger` 。 | `ScheduleTrigger` |
|**triggerEvent**| 字串 | 觸發程式的事件。 | `ScheduleTime - 2017-07-06T01:50:25Z` |
|**開始**| 字串 | 觸發程式的開始時間以 timespan UTC 格式引發。 | `2017-06-26T20:55:29.5007959Z`|
|**status**| 字串 | 顯示是否成功引發觸發程式的最終狀態。 可能的屬性值為 `Succeeded` 和 `Failed` 。 | `Succeeded`|

#### <a name="ssis-integration-runtime-log-attributes"></a>SSIS integration runtime 記錄屬性

以下是 SSIS IR 啟動/停止/維護作業的記錄屬性。

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "resultType": "",
   "properties": {
      "message": ""
   },
   "resourceId": ""
}
```

| 屬性                   | 類型   | 描述                                                   | 範例                        |
| -------------------------- | ------ | ------------------------------------------------------------- | ------------------------------ |
| **time**                   | 字串 | 事件的時間，以 UTC 格式： `YYYY-MM-DDTHH:MM:SS.00000Z` | `2017-06-28T21:00:27.3534352Z` |
| **operationName**          | 字串 | SSIS IR 操作的名稱                            | `Start/Stop/Maintenance` |
| **類別**               | 字串 | 診斷記錄的類別                               | `SSISIntegrationRuntimeLogs` |
| **correlationId**          | 字串 | 追蹤特定作業的唯一識別碼             | `f13b159b-515f-4885-9dfa-a664e949f785Deprovision0059035558` |
| **dataFactoryName**        | 字串 | ADF 的名稱                                          | `MyADFv2` |
| **integrationRuntimeName** | 字串 | SSIS IR 的名稱                                      | `MySSISIR` |
| **level**                  | 字串 | 診斷記錄層級                                  | `Informational` |
| **resultType**             | 字串 | SSIS IR 操作的結果                          | `Started/InProgress/Succeeded/Failed` |
| **message**                | 字串 | SSIS IR 操作的輸出訊息                  | `The stopping of your SSIS integration runtime has succeeded.` |
| **resourceId**             | 字串 | ADF 資源的唯一識別碼                            | `/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |

#### <a name="ssis-event-message-context-log-attributes"></a>SSIS 事件訊息內容記錄檔屬性

以下是您 SSIS IR 上 SSIS 封裝執行所產生之事件訊息的相關條件記錄檔屬性。 它們會將類似的資訊當作 [ssis 目錄 (SSISDB 傳遞) 事件訊息內容資料表或視圖](https://docs.microsoft.com/sql/integration-services/system-views/catalog-event-message-context?view=sql-server-ver15) ，以顯示許多 ssis 封裝屬性的運行時間值。 當您選取 `Basic/Verbose` 記錄層級，並在偵測/合規性檢查方面很有用時，就會產生這些問題。

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "properties": {
      "operationId": "",
      "contextDepth": "",
      "packagePath": "",
      "contextType": "",
      "contextSourceName": "",
      "contextSourceId": "",
      "propertyName": "",
      "propertyValue": ""
   },
   "resourceId": ""
}
```

| 屬性                   | 類型   | 描述                                                          | 範例                        |
| -------------------------- | ------ | -------------------------------------------------------------------- | ------------------------------ |
| **time**                   | 字串 | 事件的時間，以 UTC 格式： `YYYY-MM-DDTHH:MM:SS.00000Z`        | `2017-06-28T21:00:27.3534352Z` |
| **operationName**          | 字串 | 這會設定為 `YourSSISIRName-SSISPackageEventMessageContext`       | `mysqlmissisir-SSISPackageEventMessageContext` |
| **類別**               | 字串 | 診斷記錄的類別                                      | `SSISPackageEventMessageContext` |
| **correlationId**          | 字串 | 追蹤特定作業的唯一識別碼                    | `e55700df-4caf-4e7c-bfb8-78ac7d2f28a0` |
| **dataFactoryName**        | 字串 | ADF 的名稱                                                 | `MyADFv2` |
| **integrationRuntimeName** | 字串 | SSIS IR 的名稱                                             | `MySSISIR` |
| **level**                  | 字串 | 診斷記錄層級                                         | `Informational` |
| **operationId**            | 字串 | 用於追蹤 SSISDB 中特定作業的唯一識別碼          | `1` (1 表示與 **未** 儲存在 SSISDB/透過 t-sql 叫用的封裝相關的作業)  |
| **coNtextDepth**           | 字串 | 事件訊息內容的深度                              | `0` (0 表示封裝執行開始之前的內容，1表示發生錯誤時的內容，而且會隨著錯誤的內容而增加)  |
| **packagePath**            | 字串 | 封裝物件的路徑，做為您的事件訊息內容來源      | `\Package` |
| **coNtextType**            | 字串 | 封裝物件的類型，做為您的事件訊息內容來源      | `60` (查看 [更多內容類型](https://docs.microsoft.com/sql/integration-services/system-views/catalog-event-message-context?view=sql-server-ver15#remarks))  |
| **coNtextSourceName**      | 字串 | 封裝物件的名稱，做為您的事件訊息內容來源      | `MyPackage` |
| **coNtextSourceId**        | 字串 | 封裝物件的唯一識別碼，做為您的事件訊息內容來源 | `{E2CF27FB-EA48-41E9-AF6F-3FE938B4ADE1}` |
| **propertyName**           | 字串 | 事件訊息內容來源的封裝屬性名稱   | `DelayValidation` |
| **propertyValue**          | 字串 | 事件訊息內容來源的封裝屬性值  | `False` |
| **resourceId**             | 字串 | ADF 資源的唯一識別碼                                   | `/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |

#### <a name="ssis-event-messages-log-attributes"></a>SSIS 事件訊息記錄屬性

以下是 ssis IR 上 SSIS 封裝執行所產生事件訊息的記錄屬性。 它們會將類似的資訊傳達為 [SSISDB 事件訊息資料表或視圖](https://docs.microsoft.com/sql/integration-services/system-views/catalog-event-messages?view=sql-server-ver15) ，以顯示事件訊息的詳細文字/中繼資料。 它們是在任何記錄層級產生的，但除外 `None` 。

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "properties": {
      "operationId": "",
      "messageTime": "",
      "messageType": "",
      "messageSourceType": "",
      "message": "",
      "packageName": "",
      "eventName": "",
      "messageSourceName": "",
      "messageSourceId": "",
      "subcomponentName": "",
      "packagePath": "",
      "executionPath": "",
      "threadId": ""
   }
}
```

| 屬性                   | 類型   | 描述                                                        | 範例                        |
| -------------------------- | ------ | ------------------------------------------------------------------ | ------------------------------ |
| **time**                   | 字串 | 事件的時間，以 UTC 格式： `YYYY-MM-DDTHH:MM:SS.00000Z`      | `2017-06-28T21:00:27.3534352Z` |
| **operationName**          | 字串 | 這會設定為 `YourSSISIRName-SSISPackageEventMessages`           | `mysqlmissisir-SSISPackageEventMessages` |
| **類別**               | 字串 | 診斷記錄的類別                                    | `SSISPackageEventMessages` |
| **correlationId**          | 字串 | 追蹤特定作業的唯一識別碼                  | `e55700df-4caf-4e7c-bfb8-78ac7d2f28a0` |
| **dataFactoryName**        | 字串 | ADF 的名稱                                               | `MyADFv2` |
| **integrationRuntimeName** | 字串 | SSIS IR 的名稱                                           | `MySSISIR` |
| **level**                  | 字串 | 診斷記錄層級                                       | `Informational` |
| **operationId**            | 字串 | 用於追蹤 SSISDB 中特定作業的唯一識別碼        | `1` (1 表示與 **未** 儲存在 SSISDB/透過 t-sql 叫用的封裝相關的作業)  |
| **messageTime**            | 字串 | 以 UTC 格式建立事件訊息的時間          | `2017-06-28T21:00:27.3534352Z` |
| **messageType**            | 字串 | 事件訊息的類型                                     | `70` (查看 [更多訊息類型](https://docs.microsoft.com/sql/integration-services/system-views/catalog-operation-messages-ssisdb-database?view=sql-server-ver15#remarks))  |
| **messageSourceType**      | 字串 | 事件訊息來源的類型                              | `20` (查看 [更多訊息來源類型](https://docs.microsoft.com/sql/integration-services/system-views/catalog-operation-messages-ssisdb-database?view=sql-server-ver15#remarks))  |
| **message**                | 字串 | 事件訊息的文字                                     | `MyPackage:Validation has started.` |
| **packageName**            | 字串 | 您所執行封裝檔案的名稱                             | `MyPackage.dtsx` |
| **名稱**              | 字串 | 相關運行時間事件的名稱                                 | `OnPreValidate` |
| **messageSourceName**      | 字串 | 封裝元件的名稱，做為您的事件訊息來源         | `Data Flow Task` |
| **messageSourceId**        | 字串 | 封裝元件的唯一識別碼，做為您的事件訊息來源    | `{1a45a5a4-3df9-4f02-b818-ebf583829ad2}    ` |
| **subcomponentName**       | 字串 | 作為事件訊息來源的資料流程元件名稱       | `SSIS.Pipeline` |
| **packagePath**            | 字串 | 封裝物件的路徑，做為您的事件訊息來源            | `\Package\Data Flow Task` |
| **executionPath**          | 字串 | 從父封裝到執行之元件的完整路徑            | `\Transformation\Data Flow Task` (此路徑也會捕捉元件反覆運算)  |
| **threadId**               | 字串 | 記錄事件訊息時所執行之執行緒的唯一識別碼 | `{1a45a5a4-3df9-4f02-b818-ebf583829ad2}    ` |

#### <a name="ssis-executable-statistics-log-attributes"></a>SSIS 可執行檔統計資料記錄檔屬性

以下是 ssis IR 上 SSIS 封裝執行所產生的可執行統計資料記錄屬性，其中可執行檔是封裝控制流程中的容器或工作。 它們會將類似的資訊傳達為 [SSISDB 可執行檔統計資料表或視圖](https://docs.microsoft.com/sql/integration-services/system-views/catalog-executable-statistics?view=sql-server-ver15) ，以顯示每個執行中可執行檔的資料列，包括其反覆運算。 它們是在任何記錄層級產生的，但很適合用 `None` 來識別工作層級的瓶頸/失敗。

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "properties": {
      "executionId": "",
      "executionPath": "",
      "startTime": "",
      "endTime": "",
      "executionDuration": "",
      "executionResult": "",
      "executionValue": ""
   },
   "resourceId": ""
}
```

| 屬性                   | 類型   | 描述                                                      | 範例                        |
| -------------------------- | ------ | ---------------------------------------------------------------- | ------------------------------ |
| **time**                   | 字串 | 事件的時間，以 UTC 格式： `YYYY-MM-DDTHH:MM:SS.00000Z`    | `2017-06-28T21:00:27.3534352Z` |
| **operationName**          | 字串 | 這會設定為 `YourSSISIRName-SSISPackageExecutableStatistics`  | `mysqlmissisir-SSISPackageExecutableStatistics` |
| **類別**               | 字串 | 診斷記錄的類別                                  | `SSISPackageExecutableStatistics` |
| **correlationId**          | 字串 | 追蹤特定作業的唯一識別碼                | `e55700df-4caf-4e7c-bfb8-78ac7d2f28a0` |
| **dataFactoryName**        | 字串 | ADF 的名稱                                             | `MyADFv2` |
| **integrationRuntimeName** | 字串 | SSIS IR 的名稱                                         | `MySSISIR` |
| **level**                  | 字串 | 診斷記錄層級                                     | `Informational` |
| **executionId**            | 字串 | 用於追蹤 SSISDB 中特定執行的唯一識別碼      | `1` (1 表示與 **未** 儲存在 SSISDB/透過 t-sql 叫用的封裝相關的執行)  |
| **executionPath**          | 字串 | 從父封裝到執行之元件的完整路徑          | `\Transformation\Data Flow Task` (此路徑也會捕捉元件反覆運算)  |
| **startTime**              | 字串 | 可執行檔以 UTC 格式進入執行前階段的時間  | `2017-06-28T21:00:27.3534352Z` |
| **endTime**                | 字串 | 可執行檔以 UTC 格式進入執行後階段的時間 | `2017-06-28T21:00:27.3534352Z` |
| **executionDuration**      | 字串 | 可執行檔的執行時間（以毫秒為單位）                   | `1,125` |
| **executionResult**        | 字串 | 執行可執行檔的結果                                 | `0` (0 表示成功，1表示失敗，2表示完成，3表示取消)  |
| **>executionvalue**         | 字串 | 執行可執行檔所傳回的使用者定義值            | `1` |
| **resourceId**             | 字串 | ADF 資源的唯一識別碼                               | `/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |

#### <a name="ssis-execution-component-phases-log-attributes"></a>SSIS 執行元件階段記錄屬性

以下是 ssis IR 上 SSIS 封裝執行所產生之資料流程元件的執行時間統計資料記錄屬性。 它們會將類似的資訊傳達為 [SSISDB 執行元件階段資料表或視圖](https://docs.microsoft.com/sql/integration-services/system-views/catalog-execution-component-phases?view=sql-server-ver15) ，以顯示資料流程元件在所有執行階段所花費的時間。 當您選取 `Performance/Verbose` 記錄層級，以及用於捕獲資料流程執行統計資料時，就會產生它們。

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "properties": {
      "executionId": "",
      "packageName": "",
      "taskName": "",
      "subcomponentName": "",
      "phase": "",
      "startTime": "",
      "endTime": "",
      "executionPath": ""
   },
   "resourceId": ""
}
```

| 屬性                   | 類型   | 描述                                                         | 範例                        |
| -------------------------- | ------ | ------------------------------------------------------------------- | ------------------------------ |
| **time**                   | 字串 | 事件的時間，以 UTC 格式： `YYYY-MM-DDTHH:MM:SS.00000Z`       | `2017-06-28T21:00:27.3534352Z` |
| **operationName**          | 字串 | 這會設定為 `YourSSISIRName-SSISPackageExecutionComponentPhases` | `mysqlmissisir-SSISPackageExecutionComponentPhases` |
| **類別**               | 字串 | 診斷記錄的類別                                     | `SSISPackageExecutionComponentPhases` |
| **correlationId**          | 字串 | 追蹤特定作業的唯一識別碼                   | `e55700df-4caf-4e7c-bfb8-78ac7d2f28a0` |
| **dataFactoryName**        | 字串 | ADF 的名稱                                                | `MyADFv2` |
| **integrationRuntimeName** | 字串 | SSIS IR 的名稱                                            | `MySSISIR` |
| **level**                  | 字串 | 診斷記錄層級                                        | `Informational` |
| **executionId**            | 字串 | 用於追蹤 SSISDB 中特定執行的唯一識別碼         | `1` (1 表示與 **未** 儲存在 SSISDB/透過 t-sql 叫用的封裝相關的執行)  |
| **packageName**            | 字串 | 您所執行封裝檔案的名稱                              | `MyPackage.dtsx` |
| **taskName**               | 字串 | 執行的資料流程工作的名稱                                 | `Data Flow Task` |
| **subcomponentName**       | 字串 | 資料流程元件的名稱                                     | `Derived Column` |
| **相**                  | 字串 | 執行階段的名稱                                         | `AcquireConnections` |
| **startTime**              | 字串 | 執行時間的開始時間（UTC 格式）                  | `2017-06-28T21:00:27.3534352Z` |
| **endTime**                | 字串 | 執行時間的結束時間（UTC 格式）                    | `2017-06-28T21:00:27.3534352Z` |
| **executionPath**          | 字串 | 資料流程工作的執行路徑                            | `\Transformation\Data Flow Task` |
| **resourceId**             | 字串 | ADF 資源的唯一識別碼                                  | `/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |

#### <a name="ssis-execution-data-statistics-log-attributes"></a>SSIS 執行資料統計資料記錄屬性

以下是透過 SSIS IR 上 SSIS 封裝執行所產生的每個資料流程管線階段（從上游到下游元件），資料移動的記錄屬性。 它們會將類似的資訊傳達為 [SSISDB 執行資料統計資料表或視圖](https://docs.microsoft.com/sql/integration-services/system-views/catalog-execution-data-statistics?view=sql-server-ver15) ，以顯示透過「資料流程」工作移動之資料的資料列計數。 當您選取 `Verbose` 記錄層級，且適用于計算資料流程輸送量時，就會產生它們。

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "properties": {
      "executionId": "",
      "packageName": "",
      "taskName": "",
      "dataflowPathIdString": "",
      "dataflowPathName": "",
      "sourceComponentName": "",
      "destinationComponentName": "",
      "rowsSent": "",
      "createdTime": "",
      "executionPath": ""
   },
   "resourceId": ""
}
```

| 屬性                     | 類型   | 描述                                                        | 範例                        |
| ---------------------------- | ------ | ------------------------------------------------------------------ | ------------------------------ |
| **time**                     | 字串 | 事件的時間，以 UTC 格式： `YYYY-MM-DDTHH:MM:SS.00000Z`      | `2017-06-28T21:00:27.3534352Z` |
| **operationName**            | 字串 | 這會設定為 `YourSSISIRName-SSISPackageExecutionDataStatistics` | `mysqlmissisir-SSISPackageExecutionDataStatistics` |
| **類別**                 | 字串 | 診斷記錄的類別                                    | `SSISPackageExecutionDataStatistics` |
| **correlationId**            | 字串 | 追蹤特定作業的唯一識別碼                  | `e55700df-4caf-4e7c-bfb8-78ac7d2f28a0` |
| **dataFactoryName**          | 字串 | ADF 的名稱                                               | `MyADFv2` |
| **integrationRuntimeName**   | 字串 | SSIS IR 的名稱                                           | `MySSISIR` |
| **level**                    | 字串 | 診斷記錄層級                                       | `Informational` |
| **executionId**              | 字串 | 用於追蹤 SSISDB 中特定執行的唯一識別碼        | `1` (1 表示與 **未** 儲存在 SSISDB/透過 t-sql 叫用的封裝相關的執行)  |
| **packageName**              | 字串 | 您所執行封裝檔案的名稱                             | `MyPackage.dtsx` |
| **taskName**                 | 字串 | 執行的資料流程工作的名稱                                | `Data Flow Task` |
| **dataflowPathIdString**     | 字串 | 追蹤資料流程路徑的唯一識別碼                          | `Paths[SQLDB Table3.ADO NET Source Output]` |
| **dataflowPathName**         | 字串 | 資料流程路徑的名稱                                         | `ADO NET Source Output` |
| **sourceComponentName**      | 字串 | 傳送資料的資料流程元件名稱                    | `SQLDB Table3` |
| **destinationComponentName** | 字串 | 接收資料的資料流程元件名稱                 | `Derived Column` |
| **rowsSent**                 | 字串 | 來源元件傳送的資料列數目                        | `500` |
| **createdTime**              | 字串 | 以 UTC 格式取得資料列值的時間                | `2017-06-28T21:00:27.3534352Z` |
| **executionPath**            | 字串 | 資料流程工作的執行路徑                           | `\Transformation\Data Flow Task` |
| **resourceId**               | 字串 | ADF 資源的唯一識別碼                                 | `/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |

### <a name="log-analytics-schema"></a>Log Analytics 架構

Log Analytics 會從監視繼承架構，但有下列例外狀況：

* 每個資料行名稱中的第一個字母都是大寫。 例如，監視器中的資料行名稱 "correlationId" 是 Log Analytics 中的「CorrelationId」。
* 沒有「層級」資料行。
* 動態的 "properties" 資料行會保留為下列動態 JSON blob 類型。

    | Azure 監視器資料行 | Log Analytics 資料行 | 類型 |
    | --- | --- | --- |
    | $. 屬性。UserProperties | UserProperties | 動態 |
    | $. 屬性。注釋 | 註解 | 動態 |
    | $. 屬性。輸入 | 輸入 | 動態 |
    | $. 屬性。輸出 | 輸出 | 動態 |
    | $. 屬性。錯誤。 errorCode | ErrorCode | int |
    | $. 屬性。錯誤。訊息 | ErrorMessage | 字串 |
    | $. 屬性。錯誤 | 錯誤 | 動態 |
    | $. 屬性。前輩 | 前輩 | 動態 |
    | $. 屬性。參數 | 參數 | 動態 |
    | $.properties.SystemParameters | SystemParameters | 動態 |
    | $. 屬性。標籤 | 標籤 | 動態 |

## <a name="monitor-ssis-operations-with-azure-monitor"></a>使用 Azure 監視器監視 SSIS 作業

若要增益 & 轉移您的 SSIS 工作負載，您可以 [在 ADF 中布建 SSIS IR](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure) ，其支援：

- 執行已部署到 SSIS 目錄 (SSISDB) 的套件，此目錄由 Azure SQL Database 伺服器/受控執行個體 (專案部署模型) 裝載
- 執行已部署到 Azure SQL 受控執行個體 (套件部署模型) 所裝載檔案系統、Azure 檔案儲存體或 SQL Server 資料庫 (MSDB) 中的套件

布建之後，您可以 [使用 Azure PowerShell 或 ADF 入口網站的 **監視** 中樞來檢查 SSIS IR 操作狀態](https://docs.microsoft.com/azure/data-factory/monitor-integration-runtime#azure-ssis-integration-runtime)。 使用專案部署模型時，SSIS 封裝執行記錄會儲存在 SSISDB 內部資料表或查看中，因此您可以使用 SSMS 之類的指定工具來查詢、分析並以視覺化方式呈現它們。 使用套件部署模型，SSIS 封裝執行記錄可以儲存在檔案系統中，或 Azure 檔案儲存體為 CSV 檔案，您仍然需要使用其他指定的工具進行剖析和處理，才能進行查詢、分析並以視覺化方式呈現它們。

現在有了 [Azure 監視器](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform) 整合，您就可以查詢、分析並以視覺化方式呈現從 SSIS IR 作業產生的所有計量和記錄，以及在 Azure 入口網站上執行 ssis 套件。 此外，您也可以在這些警示上引發警示。

### <a name="configure-diagnostic-settings-and-workspace-for-ssis-operations"></a>設定 SSIS 作業的診斷設定和工作區

若要將從 SSIS IR 作業和 SSIS 套件執行產生的所有計量和記錄傳送至 Azure 監視器，您必須 [設定 ADF 的診斷設定和工作區](https://docs.microsoft.com/azure/data-factory/monitor-using-azure-monitor#configure-diagnostic-settings-and-workspace)。

### <a name="ssis-operational-metrics"></a>SSIS 操作計量

SSIS 操作 [計量](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics) 是效能計數器或數值，可描述 ssis IR 啟動和停止作業的狀態，以及在特定時間點的 ssis 封裝執行。 它們是 [Azure 監視器中 ADF 計量](https://docs.microsoft.com/azure/data-factory/monitor-using-azure-monitor#data-factory-metrics)的一部分。

當您在 Azure 監視器上設定 ADF 的診斷設定和工作區時，選取 [ _AllMetrics_ ] 核取方塊將會 [使用 Azure 計量瀏覽器](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started)、 [azure 儀表板上的簡報](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-app-dashboards)和 [近乎即時的警示](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric)，提供 SSIS 操作計量以進行互動式分析。

![命名您的設定，然後選取記錄分析工作區](media/data-factory-monitor-oms/monitor-oms-image2.png)

### <a name="ssis-operational-alerts"></a>SSIS 操作警示

若要從 ADF 入口網站發出 SSIS 操作計量的警示，請[選取 Adf**監視器**中樞的 [**警示 & 計量**] 頁面，並依照提供的逐步指示](https://docs.microsoft.com/azure/data-factory/monitor-visually#alerts)進行。

![從 ADF 入口網站引發 SSIS 操作警示](media/data-factory-monitor-oms/data-factory-monitor-alerts-ssis.png)

若要從 Azure 入口網站發出 SSIS 操作計量的警示，請[選取 Azure**監視器**中樞的 [**警示**] 頁面，並依照提供的逐步指示](https://docs.microsoft.com/azure/data-factory/monitor-using-azure-monitor#data-factory-alerts)進行。

![從 Azure 入口網站引發 SSIS 操作警示](media/data-factory-monitor-oms/azure-monitor-alerts-ssis.png)

### <a name="ssis-operational-logs"></a>SSIS 操作記錄

SSIS 作業 [記錄](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs) 是 ssis IR 作業和 ssis 封裝執行所產生的事件，可針對任何已識別的問題提供足夠的內容，而且對於根本原因分析很有用。 

當您在 Azure 監視器上設定 ADF 的診斷設定和工作區時，您可以選取相關的 SSIS 操作記錄，並將它們傳送至以 Azure 資料總管為基礎的 Log Analytics。 在那裡，您可以 [使用豐富的查詢語言](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview)、 [在 Azure 儀表板上呈現](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-app-dashboards)，以及 [近乎即時的警示](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log)，來進行分析。

![命名您的設定，然後選取記錄分析工作區](media/data-factory-monitor-oms/monitor-oms-image2.png)

Azure 監視器與 Log Analytics 中的 SSIS 封裝執行記錄的架構和內容，與 SSISDB 內部資料表或視圖的架構類似。

| Azure 監視器記錄類別          | Log Analytics 資料表                     | SSISDB 內部資料表/視圖              |
| ------------------------------------- | ---------------------------------------- | ----------------------------------------- |
| `SSISIntegrationRuntimeLogs`          | `ADFSSISIntegrationRuntimeLogs`          |                                           |
| `SSISPackageEventMessageContext`      | `ADFSSISPackageEventMessageContext`      | `[internal].[event_message_context]`      |
| `SSISPackageEventMessages`            | `ADFSSISPackageEventMessages`            | `[internal].[event_messages]`             |
| `SSISPackageExecutableStatistics`     | `ADFSSISPackageExecutableStatistics`     | `[internal].[executable_statistics]`      |
| `SSISPackageExecutionComponentPhases` | `ADFSSISPackageExecutionComponentPhases` | `[internal].[execution_component_phases]` |
| `SSISPackageExecutionDataStatistics`  | `ADFSSISPackageExecutionDataStatistics`  | `[internal].[execution_data_statistics]`  |

如需 SSIS 操作記錄屬性/屬性的詳細資訊，請參閱 [適用于 ADF 的 Azure 監視器和 Log Analytics 架構](https://docs.microsoft.com/azure/data-factory/monitor-using-azure-monitor#schema-of-logs-and-events)。

您所選取的 SSIS 套件執行記錄一律會傳送至 Log Analytics，而不論其調用方法為何。 例如，您可以透過 SSMS 上的 T-sql、SQL Server Agent 或其他指定的工具，在啟用 Azure 的 SSDT 上叫用封裝，以及在 ADF 管線中觸發或偵測執行 SSIS 套件活動的執行。

在記錄分析上查詢 SSIS IR 作業記錄時，您可以分別使用設定為和的 **OperationName** 和 **ResultType** 屬性 `Start/Stop/Maintenance` `Started/InProgress/Succeeded/Failed` 。 

![查詢 Log Analytics 上的 SSIS IR 作業記錄](media/data-factory-monitor-oms/log-analytics-query.png)

在記錄分析上查詢 SSIS 封裝執行記錄時，您可以使用**OperationId** / **ExecutionId** / **CorrelationId**屬性來聯結它們。 **OperationId** /**ExecutionId** `1` 對於所有與**未**儲存在 SSISDB/透過 t-sql 叫用之套件相關的作業/執行，一律會將 ExecutionId 設定為。

![查詢 Log Analytics 上的 SSIS 套件執行記錄](media/data-factory-monitor-oms/log-analytics-query2.png)

## <a name="next-steps"></a>後續步驟
[以程式設計方式監視和管理管線](monitor-programmatically.md)
