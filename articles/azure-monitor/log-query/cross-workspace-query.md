---
title: 使用 Azure 監視器跨資源查詢 | Microsoft Docs
description: 本文說明如何針對來自訂用帳戶中多個工作區和 App Insights 應用程式的資源執行查詢。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/01/2020
ms.openlocfilehash: e2f9430ae039cc54c3e6180eb8ea76791d17f67f
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91285123"
---
# <a name="perform-log-query-in-azure-monitor-that-span-across-workspaces-and-apps"></a>在跨工作區和應用程式的 Azure 監視器中執行記錄查詢

Azure 監視器記錄在相同的資源群組、另一個資源群組或另一個訂用帳戶中，支援跨多個 Log Analytics 工作區和 Application Insights 應用程式進行查詢。 這讓您對資料能有全系統的檢視。

有兩種方法可以查詢儲存在多個工作區和應用程式中的資料：
1. 明確地指定工作區和應用程式詳細資料。 這項技術詳述于本文中。
2. 隱含地使用 [資源內容查詢](../platform/design-logs-deployment.md#access-mode)。 當您在特定資源、資源群組或訂用帳戶的內容中進行查詢時，將會從包含這些資源資料的所有工作區提取相關的資料。 將不會提取儲存在應用程式中的 Application Insights 資料。

> [!IMPORTANT]
> 如果您使用以 [工作區為基礎的 Application Insights 資源](../app/create-workspace-resource.md) 遙測會與所有其他記錄資料儲存在 Log Analytics 工作區中。 使用 log ( # A1 運算式來撰寫查詢，其中包含多個工作區中的應用程式。 針對相同工作區中的多個應用程式，您不需要跨工作區查詢。


## <a name="cross-resource-query-limits"></a>跨資源查詢限制 

* 您可以包含在單一查詢中的 Application Insights 資源與 Log Analytics 工作區數目限制為100。
* View Designer 不支援跨資源查詢。 您可以在 Log Analytics 中撰寫查詢，並將其釘選到 Azure 儀表板，以 [視覺化方式呈現記錄查詢](../learn/tutorial-logs-dashboards.md)。 
* 只有目前的 [SCHEDULEDQUERYRULES API](/rest/api/monitor/scheduledqueryrules)支援記錄警示中的跨資源查詢。 如果您使用舊版 Log Analytics 警示 API，您必須 [切換到目前的 api](../platform/alerts-log-api-switch.md)。


## <a name="querying-across-log-analytics-workspaces-and-from-application-insights"></a>跨 Log Analytics 工作區和從 Application Insights 查詢
若要在查詢中參考另一個工作區，請使用 [*workspace*](./workspace-expression.md) 識別項，而若要查詢來自 Application Insights 的應用程式，請使用 [*app*](./app-expression.md) 識別項。  

### <a name="identifying-workspace-resources"></a>識別工作區資源
下列範例示範對 Log Analytics 工作區的查詢，從名為 contosoretail-it** 工作區上的 Update 資料表，傳回記錄彙總計數。 

您可以下列數種方式之一來完成識別工作區：

* 資源名稱 - 是人類看得懂的工作區名稱，有時稱為「元件名稱」**。 

    `workspace("contosoretail-it").Update | count`

* 限定名稱-是工作區的「完整名稱」，由訂用帳戶名稱、資源群組及元件名稱所組成，格式如下： *subscriptionName/resourceGroup/componentName*。 

    `workspace('contoso/contosoretail/contosoretail-it').Update | count`

    >[!NOTE]
    >由於 Azure 訂用帳戶名稱並非唯一，因此這個識別碼可能語意模糊。 
    >

* 工作區識別碼 - 工作區識別碼是指派給每個工作區的唯一、不可變識別碼，會以全域唯一識別碼 (GUID) 來表示。

    `workspace("b459b4u5-912x-46d5-9cb1-p43069212nb4").Update | count`

* Azure 資源識別碼 – Azure 定義的工作區唯一身分識別。 當資源名稱語意模糊時，您可以使用資源識別碼。  就工作區而言，格式為：*/subscriptions/subscriptionId/resourcegroups/resourceGroup/providers/microsoft.OperationalInsights/workspaces/componentName*。  

    例如：
    ``` 
    workspace("/subscriptions/e427519-5645-8x4e-1v67-3b84b59a1985/resourcegroups/ContosoAzureHQ/providers/Microsoft.OperationalInsights/workspaces/contosoretail-it").Update | count
    ```

### <a name="identifying-an-application"></a>識別應用程式
下列範例會傳回對 Application Insights 中名為 *fabrikamapp* 的應用程式所進行要求的彙總計數。 

在 Application Insights 中識別應用程式可以使用 *app(Identifier)* 運算式來完成。  *Identifier* 引數會使用下列其中之一指定應用程式：

* 資源名稱 - 是人類看得懂的應用程式名稱，有時稱為「元件名稱」**。  

    `app("fabrikamapp")`

    >[!NOTE]
    >依名稱識別應用程式會在所有可存取的訂用帳戶中採用唯一性。 如果您有多個應用程式具有該指定名稱，查詢就會因為語意模糊而失敗。 在此情況下，您必須使用其中一種其他識別碼。

* 限定名稱 - 是應用程式的「完整名稱」，由訂用帳戶名稱、資源群組及元件名稱所組成，格式如下：*subscriptionName/resourceGroup/componentName*。 

    `app("AI-Prototype/Fabrikam/fabrikamapp").requests | count`

     >[!NOTE]
    >由於 Azure 訂用帳戶名稱並非唯一，因此這個識別碼可能語意模糊。 
    >

* 識別碼 - 應用程式的應用程式 GUID。

    `app("b459b4f6-912x-46d5-9cb1-b43069212ab4").requests | count`

* Azure 資源識別碼 – Azure 定義的應用程式唯一身分識別。 當資源名稱語意模糊時，您可以使用資源識別碼。 格式為：*/subscriptions/subscriptionId/resourcegroups/resourceGroup/providers/microsoft.OperationalInsights/components/componentName*。  

    例如：
    ```
    app("/subscriptions/b459b4f6-912x-46d5-9cb1-b43069212ab4/resourcegroups/Fabrikam/providers/microsoft.insights/components/fabrikamapp").requests | count
    ```

### <a name="performing-a-query-across-multiple-resources"></a>跨多個資源執行查詢
您可以查詢任何資源執行個體的多個資源，這些資源執行個體可以是合併的工作區和應用程式。
    
跨兩個工作區的查詢範例：    

```
union Update, workspace("contosoretail-it").Update, workspace("b459b4u5-912x-46d5-9cb1-p43069212nb4").Update
| where TimeGenerated >= ago(1h)
| where UpdateState == "Needed"
| summarize dcount(Computer) by Classification
```

## <a name="using-cross-resource-query-for-multiple-resources"></a>使用多個資源的跨資源查詢
使用跨資源查詢將資料從多個 Log Analytics 工作區和 Application Insights 資源相互關聯時，查詢可能會變得複雜且難以維護。 您應該利用 [Azure 監視器記錄查詢中的函式](functions.md)，將查詢邏輯與查詢資源的範圍分開，如此可簡化查詢結構。 下列範例示範如何監視多個 Application Insights 資源，並將失敗的要求計數依應用程式名稱視覺化。 

建立參考 Application Insights 資源範圍的查詢，如下所示。 `withsource= SourceApp` 命令會新增一個資料行，此資料行可指定傳送記錄的應用程式名稱。 [將查詢儲存為函式](functions.md#create-a-function)，且別名為 _applicationsScoping_。

```Kusto
// crossResource function that scopes my Application Insights resources
union withsource= SourceApp
app('Contoso-app1').requests, 
app('Contoso-app2').requests,
app('Contoso-app3').requests,
app('Contoso-app4').requests,
app('Contoso-app5').requests
```



您現在可以在跨資源查詢中[使用此函式](./functions.md#use-a-function)，如下所示。 函式別名 _applicationsScoping_ 會從所有定義的應用程式傳送要求資料表的聯集。 接著，查詢會篩選失敗的要求，並依應用程式將趨勢視覺化。 在此範例中，_parse_ 運算子是選用的。 它會從 _SourceApp_ 屬性擷取應用程式名稱。

```Kusto
applicationsScoping 
| where timestamp > ago(12h)
| where success == 'False'
| parse SourceApp with * '(' applicationName ')' * 
| summarize count() by applicationName, bin(timestamp, 1h) 
| render timechart
```

>[!NOTE]
> 因為警示規則資源（包括工作區和應用程式）的存取驗證是在警示建立期間執行，所以此方法無法與記錄警示一起使用。 不支援在建立警示之後，將新的資源新增至函式。 如果您想要在記錄警示中使用資源範圍的函式，您需要在入口網站中編輯警示規則，或使用 Resource Manager 範本來更新已設定範圍的資源。 或者，您也可以在記錄警示查詢中包含資源清單。


![時間圖](media/cross-workspace-query/chart.png)

## <a name="next-steps"></a>後續步驟

- 檢閱[分析 Azure 監視器中的記錄資料](log-query-overview.md)，了解記錄查詢的簡短概觀，以及建置 Azure 監視器記錄資料結構的方式。
- 檢閱 [Azure 監視器記錄查詢](query-language.md)，以檢視所有 Azure 監視器記錄查詢的資源。

