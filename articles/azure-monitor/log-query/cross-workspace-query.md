---
title: 使用 Azure 監視器跨資源查詢 | Microsoft Docs
description: 本文說明如何針對來自訂用帳戶中多個工作區和 App Insights 應用程式的資源執行查詢。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/05/2019
ms.openlocfilehash: 4740034bd970f42833125fa43bfdf72f710ac147
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249603"
---
# <a name="perform-cross-resource-log-queries-in-azure-monitor"></a>在 Azure 監視器中執行跨資源記錄查詢  

先前使用 Azure 監視器時，您只能分析來自目前工作區內的資料，這項限制讓您無法跨訂用帳戶中定義的多個工作區執行查詢。  此外，您只能搜尋在 Application Insights 中或從 Visual Studio 直接使用 Application Insights 從 Web 應用程式收集的遙測項目。 如此一來，也難以將作業和應用程式資料一起以原生方式分析。

現在您不僅可以跨多個 Log Analytics 工作區查詢，也可以查詢來自相同資源群組、另一個資源群組或其他訂用帳戶中特定 Application Insights 應用程式中的資料。 這讓您對資料能有全系統的檢視。 您只能在 [Log Analytics](portals.md) 中執行這些類型的查詢。

## <a name="cross-resource-query-limits"></a>跨資源查詢限制 

* 可在單個查詢中包含的應用程式見解資源和日誌分析工作區的數量限制為 100。
* 視圖設計器不支援跨資源查詢。 您可以在日誌分析中創作查詢，並將其固定到 Azure 儀表板以[視覺化日誌查詢](../learn/tutorial-logs-dashboards.md)。 
* 新的 [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) 支援記錄警示中的跨資源查詢。 根據預設，Azure 監視器會使用[舊版 Log Analytics 警示 API](../platform/api-alerts.md) 從 Azure 入口網站建立新的記錄警示規則，除非您從[舊版記錄警示 API](../platform/alerts-log-api-switch.md#process-of-switching-from-legacy-log-alerts-api) 切換。 切換之後，新的 API 將成為 Azure 入口網站中新警示規則的預設值，並允許您建立跨資源查詢記錄警示規則。 您可以使用[計畫查詢規則 API 的 Azure 資源管理器範本](../platform/alerts-log.md#log-alert-with-cross-resource-query-using-azure-resource-template)創建跨資源查詢日誌警報規則，而無需進行切換，但此警報規則可以通過[計畫查詢規則 API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)而不是從 Azure 門戶進行管理。


## <a name="querying-across-log-analytics-workspaces-and-from-application-insights"></a>跨 Log Analytics 工作區和從 Application Insights 查詢
若要在查詢中參考另一個工作區，請使用 [*workspace*](https://docs.microsoft.com/azure/log-analytics/query-language/workspace-expression) 識別項，而若要查詢來自 Application Insights 的應用程式，請使用 [*app*](https://docs.microsoft.com/azure/log-analytics/query-language/app-expression) 識別項。  

### <a name="identifying-workspace-resources"></a>識別工作區資源
下列範例示範對 Log Analytics 工作區的查詢，從名為 contosoretail-it** 工作區上的 Update 資料表，傳回記錄彙總計數。 

您可以下列數種方式之一來完成識別工作區：

* 資源名稱 - 是人類看得懂的工作區名稱，有時稱為「元件名稱」**。 

    `workspace("contosoretail-it").Update | count`

* 限定名稱 - 是工作區的"全名"，由此格式的訂閱名稱、資源組和元件名稱組成：*訂閱名稱/資源組/元件名稱*。 

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
    >按名稱標識應用程式假定所有可訪問的訂閱都是唯一的。 如果您有多個應用程式具有該指定名稱，查詢就會因為語意模糊而失敗。 在此情況下，您必須使用其中一種其他識別碼。

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



您現在可以在跨資源查詢中[使用此函式](../../azure-monitor/log-query/functions.md#use-a-function)，如下所示。 函式別名 _applicationsScoping_ 會從所有定義的應用程式傳送要求資料表的聯集。 接著，查詢會篩選失敗的要求，並依應用程式將趨勢視覺化。 在此範例中，_parse_ 運算子是選用的。 它會從 _SourceApp_ 屬性擷取應用程式名稱。

```Kusto
applicationsScoping 
| where timestamp > ago(12h)
| where success == 'False'
| parse SourceApp with * '(' applicationName ')' * 
| summarize count() by applicationName, bin(timestamp, 1h) 
| render timechart
```

>[!NOTE]
>此方法不能與日誌警報一起使用，因為警報規則資源（包括工作區和應用程式）的訪問驗證是在警報創建時執行的。 不支援在創建警報後向函數添加新資源。 如果希望使用函數在日誌警報中定義資源，則需要在門戶中編輯警報規則，或者使用資源管理器範本來更新作用域資源。 或者，您可以在日誌警報查詢中包含資源清單。


![時間圖](media/cross-workspace-query/chart.png)

## <a name="next-steps"></a>後續步驟

- 檢閱[分析 Azure 監視器中的記錄資料](log-query-overview.md)，了解記錄查詢的簡短概觀，以及建置 Azure 監視器記錄資料結構的方式。
- 檢閱 [Azure 監視器記錄查詢](query-language.md)，以檢視所有 Azure 監視器記錄查詢的資源。
