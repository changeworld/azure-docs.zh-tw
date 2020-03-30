---
title: 管理 Azure 監視器日誌的使用和成本 |微軟文檔
description: 瞭解如何更改定價計畫並管理 Azure 監視器中的日誌分析工作區的資料量和保留原則。
services: azure-monitor
documentationcenter: azure-monitor
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: bwren
ms.subservice: ''
ms.openlocfilehash: 6911afa5dfcd14f9e5d1068acbcb2355200c5545
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79479802"
---
# <a name="manage-usage-and-costs-with-azure-monitor-logs"></a>使用 Azure 監視器日誌管理使用方式和成本

> [!NOTE]
> 本文介紹如何理解和控制 Azure 監視器日誌的成本。 相關文章"[監視使用方式和估計成本](https://docs.microsoft.com/azure/azure-monitor/platform/usage-estimated-costs)"介紹了如何查看不同定價模型的多個 Azure 監視功能的使用方式和估計成本。

> [!NOTE]
> 本文中顯示的所有價格和成本僅供參考。 

Azure 監視器日誌旨在縮放和支援每天從企業中的任何源或部署在 Azure 中收集、索引和存儲大量資料。  雖然這可能是您組織的主要推動力，但成本效率終究是最基本的推動力。 為此，請務必瞭解，Log Analytics 工作區的成本不僅基於收集的資料量，還取決於所選計畫以及您選擇存儲從連接源生成的資料的時間。  

在本文中，我們將介紹如何主動監視引入的資料量和存儲增長，並定義控制這些相關成本的限制。 

## <a name="pricing-model"></a>定價模式

日誌分析的預設定價是基於引入的資料量的**即用即付**模型，並可選擇用於延長資料保留時間。 資料量測量為要存儲的資料的大小。 每個日誌分析工作區都作為單獨的服務收費，並有助於為 Azure 訂閱支付帳單。 資料引入量可能相當可觀，具體取決於以下因素： 

  - 啟用的管理解決方案數量及其配置（例如 
  - 被監視的 VM 數量
  - 從每個受監視的 VM 收集的資料類型 
  
除了即用即付模式外，日誌分析還具有**容量預留**層，與即用即付價格相比，您可以節省多達 25% 的費用。 容量預訂定價使您能夠從 100 GB/天開始購買預訂。 任何超出預訂級別的使用量都將以即用即付費率計費。 容量預留層有 31 天的承諾期。 在承諾期內，您可以更改為更高級別的容量預留層（這將重新開機 31 天承諾期），但在承諾期之後，您不能返回即用即付或較低的容量預留層完成。 
[詳細瞭解](https://azure.microsoft.com/pricing/details/monitor/)日誌分析即用即付和容量預留定價。 

在所有定價層中，資料量都是在準備存儲時從資料的字串表示形式計算的。 計算事件大小（`_ResourceId`包括`_ItemId`、`_IsBillable`和`_BilledSize`） 不包括[所有資料類型共有的幾個屬性](https://docs.microsoft.com/azure/azure-monitor/platform/log-standard-properties)。

此外，請注意，某些解決方案（如[Azure 安全中心和](https://azure.microsoft.com/pricing/details/security-center/) [Azure Sentinel）](https://azure.microsoft.com/pricing/details/azure-sentinel/)有自己的定價模型。 

## <a name="estimating-the-costs-to-manage-your-environment"></a>估算管理環境的成本 

如果尚未使用 Azure 監視器日誌，則可以使用[Azure 監視器定價計算機](https://azure.microsoft.com/pricing/calculator/?service=monitor)來估計使用日誌分析的成本。 首先在"搜索"框中輸入"Azure 監視器"，然後按一下生成的 Azure 監視器磁貼。 向下滾動頁面到 Azure 監視器，然後從"類型"下拉下下拉清單選擇"日誌分析"。  在這裡，您可以輸入要從每個 VM 收集的 VM 數量和 GB 資料。 通常從典型的 Azure VM 中攝錄 1 到 3 GB 的資料月。 如果您已經正在評估 Azure 監視器日誌，則可以使用您自己的環境中的資料統計資訊。 有關如何確定[受監視 VM 的數量](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-nodes-sending-data)以及[工作區正在攝用的資料量](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-ingested-data-volume)，請參閱下文。 

## <a name="understand-your-usage-and-estimate-costs"></a>瞭解您的使用方式並估算成本

如果您現在使用的是 Azure 監視器日誌，則很容易瞭解成本可能基於最近的使用模式。 為此，請使用**日誌分析使用方式和估計成本**來查看和分析資料使用方式。 這將顯示每個解決方案收集的資料量、保留的資料量以及基於引入的資料量和超出包含數量的任何其他保留率對成本的估計。

![使用量和估計成本](media/manage-cost-storage/usage-estimated-cost-dashboard-01.png)

若要更詳細地探索您的資料，請在 [使用量和估計成本]**** 頁面上，按一下位於任一圖表右上方的圖示。 現在您可以使用此查詢來探索更詳細的使用量。  

![記錄檢視](media/manage-cost-storage/logs.png)

您可以從 [使用量和估計成本]**** 頁面檢閱當月的資料量。 這之中包括 Log Analytics 工作區中接收和保留的所有資料。  按一下頁面頂部的**使用方式詳細資訊**，查看使用方式儀表板，並查看有關資料量趨勢的資訊（按源、電腦和產品/服務）。 若要檢視和設定每日上限或修改保留期限，請按一下 [資料量管理]****。
 
Log Analytics 費用會新增到您的 Azure 帳單中。 您可以在 Azure 入口網站中的 [帳務] 區段下，或在 [Azure 計費入口網站](https://account.windowsazure.com/Subscriptions)中，查看 Azure 帳單的詳細資料。  

## <a name="viewing-log-analytics-usage-on-your-azure-bill"></a>查看 Azure 帳單上的日誌分析使用方式 

Azure 在[Azure 成本管理和計費](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis?toc=/azure/billing/TOC.json)中心中提供了大量有用的功能。 例如，"成本分析"功能使您能夠查看 Azure 資源的支出。 按資源類型（向 Microsoft.操作見解/日誌分析工作區）添加篩選器將允許您跟蹤支出。

[從 Azure 門戶下載使用方式](https://docs.microsoft.com/azure/billing/billing-download-azure-invoice-daily-usage-date#download-usage-in-azure-portal)可以更好地瞭解使用方式。 在下載的試算表中，您可以看到每個 Azure 資源（例如日誌分析工作區）每天的使用方式。 在此 Excel 試算表中，首先在"儀錶類別"列上篩選"見解和分析"（由某些遺留定價層使用）和"日誌分析"，然後在"實例"上添加篩選器，即可找到日誌分析工作區中的使用方式ID"列，即"包含工作區"。 用法顯示在"消耗量"列中，每個條目的單位顯示在"度量單位"列中。  有關詳細資訊，可説明您瞭解 Microsoft [Azure 帳單](https://docs.microsoft.com/azure/billing/billing-understand-your-bill)。 

## <a name="changing-pricing-tier"></a>正在變更定價層

要更改工作區的日誌分析定價層， 

1. 在 Azure 門戶中，打開**工作區中的"使用方式"和估計成本**，您將看到每個可用於此工作區的定價層的清單。

2. 查看每個定價層的估計成本。 此估計值基於最近 31 天的使用方式，因此此成本估算依賴于過去 31 天代表您的典型使用方式。 在下面的示例中，您可以看到，與 100 GB/天容量預留層 （#2）相比，根據過去 31 天的資料模式，此工作空間在即用即付層 （#1） 中的成本更低。  

    ![定價層](media/manage-cost-storage/pricing-tier-estimated-costs.png)

3. 根據最近 31 天的使用方式查看估計成本後，如果您決定更改定價層，請按一下 **"選擇**"。  

您還可以使用`sku`參數（在 Azure 資源管理器範本中）`pricingTier`[通過 Azure 資源管理器設置定價層](https://docs.microsoft.com/azure/azure-monitor/platform/template-workspace-configuration#configure-a-log-analytics-workspace)。 

## <a name="legacy-pricing-tiers"></a>舊版定價層

在 2018 年 4 月 2 日之前在其中具有日誌分析工作區或應用程式見解資源的訂閱，或連結到 2019 年 2 月 1 日之前啟動的企業協定，將繼續有權訪問使用舊版定價層：**免費**、**獨立（每 GB）** 和**每個節點 （OMS）。**  免費定價層中的工作區的每日資料引入量將限制為 500 MB（Azure 安全中心收集的安全資料類型除外），並且資料保留期限制為 7 天。 免費定價層僅用於評估目的。 獨立或每個節點定價層中的工作區的使用者可配置保留期長達 2 年。 

2016 年 4 月之前創建的工作區還可以訪問原始**標準**定價層和**高級**定價層，這些層的固定資料保留天數分別為 30 天和 365 天。 無法在**標準**或**高級**定價層中創建新工作區，如果工作區移出這些層，則無法將其移回。 

有關定價層限制的更多詳細資訊，[請點擊此處](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#log-analytics-workspaces)。

> [!NOTE]
> 若要使用來自購買 OMS E1套件、OMS E2 套件或 OMS Add-On for System Center 的權利，請選擇 Log Analytics [每個節點]** 定價層。

## <a name="change-the-data-retention-period"></a>變更資料保留期

下列步驟說明如何設定您工作區中的記錄資料保留時間。

### <a name="default-retention"></a>預設保留

要設置工作區的預設保留，請 
 
1. 在 Azure 門戶中，從工作區中，從左側窗格中選擇 **"使用方式"和"估計成本**"。
2. 在 [使用量和估計成本]**** 頁面上，按一下頁面頂端的 [資料量管理]****。
3. 在窗格上，移動滑桿來增加或減少天數，然後按一下 [確定]****。  如果您位於「免費」** 層，將無法修改資料保留期，必須升級至付費層，才能控制此設定。

    ![變更工作區資料保留期設定](media/manage-cost-storage/manage-cost-change-retention-01.png)
    
還可以使用`retentionInDays`參數[通過 Azure 資源管理器設置](https://docs.microsoft.com/azure/azure-monitor/platform/template-workspace-configuration#configure-a-log-analytics-workspace)保留。 此外，如果將資料保留設置為 30 天，則可以使用`immediatePurgeDataOn30Days`參數立即觸發舊資料清除，這對於合規性相關方案可能很有用。 此功能僅通過 Azure 資源管理器公開。 

預設情況下，兩種`Usage`資料類型`AzureActivity`（和 -- ） 將保留 90 天，並且此 90 天的保留不收取任何費用。 這些資料類型也不含資料引入費用。 

### <a name="retention-by-data-type"></a>按資料類型保留

還可以為各個資料類型指定不同的保留設置。 每種資料類型都是工作區的子資源。 例如，在[Azure 資源管理器](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)中，可以將安全事件表定址為：

```
/subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent
```

請注意，資料類型（表）區分大小寫。  要獲取特定資料類型的當前每個資料類型保留設置（在此示例中為安全事件），請使用：

```JSON
    GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview
```

要獲取工作區中所有資料類型的當前每個資料類型保留設置，只需省略特定資料類型，例如：

```JSON
    GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables?api-version=2017-04-26-preview
```

要將特定資料類型的保留時間（在此示例中為安全事件）設置為 730 天，執行

```JSON
    PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview
    {
        "properties": 
        {
            "retentionInDays": 730
        }
    }
```

不能`Usage`使用`AzureActivity`自訂保留設置 和 資料類型。 它們將承擔預設工作區保留的最大值或 90 天。 

OSS 工具[ARMclient](https://github.com/projectkudu/ARMClient)是直接連接到 Azure 資源管理器以按資料類型設置保留情況的一個很好的工具。  從[大衛·埃博](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html)和[丹尼爾·鮑比斯](https://blog.bowbyes.co.nz/2016/11/02/using-armclient-to-directly-access-azure-arm-rest-apis-and-list-arm-policy-details/)的文章瞭解更多關於ARMclient。  下面是使用 ARMClient 的示例，將安全事件資料設置為 730 天保留：

```
armclient PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview "{properties: {retentionInDays: 730}}"
```

> [!NOTE]
> 設置單個資料類型的保留率可用於降低資料保留成本。  對於從 2019 年 10 月開始收集的資料（發佈此功能時），減少某些資料類型的保留率可能會隨著時間的推移降低您的保留成本。  對於之前收集的資料，為單個類型設置較低的保留率不會影響您的保留成本。  

## <a name="manage-your-maximum-daily-data-volume"></a>管理最大日資料量

您可以為工作區設定每日上限和限制每日擷取，但請謹慎使用，因為您的目標是不要達到每日限制。  否則，您會遺失當天其餘時間的資料，這可能會影響其功能取決於工作區中一直在提供的最新資料的其他 Azure 服務和解決方案。  如此一來，您在資源健全狀況支援 IT 服務時觀察和接收警示的功能會受到影響。  每日上限旨在用作管理來自託管資源的資料量意外增長並保持在限制範圍內的一種方式，或者當您希望限制工作區的計畫外費用時。  

若達到每日限制，將停止收集當天剩餘時間的需計費資料類型。 在所選取的 Log Analytics 工作區中，頁面頂端會出現警告橫幅，且系統會將作業事件傳送至 **LogManagement** 類別下的「Operation」** 資料表。 一旦過了「每日限制的設定時間」** 下定義的重設時間後，資料收集就會繼續執行。 我們建議您根據此作業事件定義警示規則，設定為在達到每日資料限制時發出通知。 

> [!NOTE]
> 每日上限不會停止從 Azure 安全中心收集資料，但 2017 年 6 月 19 日之前安裝 Azure 安全中心的工作區除外。 

> [!NOTE]
> 應用每日上限時固有的延遲可能意味著不會精確地應用上限，以精確地應用指定的每日上限級別。 

### <a name="identify-what-daily-data-limit-to-define"></a>識別要定義的每日資料限制

檢閱 [Log Analytics 使用量和估計成本](usage-estimated-costs.md)，以了解資料擷取趨勢及要定義的每日資料量上限。 應該謹慎考慮它，因為達到限制後您將無法監視資源。 

### <a name="set-the-daily-cap"></a>設置每日上限

以下步驟描述如何配置限制來管理 Log Analytics 工作區每天將引入的資料量。  

1. 在工作區中，從左側窗格中選取 [使用量和估計成本]****。
2. 在所選工作區的 [使用量和估計成本]**** 頁面上，按一下頁面頂端的 [資料量管理]****。 
3. 預設情況下，每日上限為 **"關閉**"，按一下 **"打開**"以啟用它，然後以 GB/天設置資料量限制。

    ![Log Analytics 設定資料限制](media/manage-cost-storage/set-daily-volume-cap-01.png)

### <a name="alert-when-daily-cap-reached"></a>每日上限達到時的警報

雖然我們會在您達到資料限制閾值時，於 Azure 入口網站中顯示視覺提示，但對於需要立刻處理的作業問題，此行為並不一定與您的管理方式一致。  若要接收警示通知，您可以在 Azure 監視器中建立新的警示規則。  要瞭解更多資訊，請參閱[如何創建、查看和管理警報](alerts-metric.md)。

為協助您開始使用，以下是警示的建議設定：

- 目標：選取您的 Log Analytics 資源
- 準則： 
   - 訊號名稱：自訂記錄搜尋
   - 搜尋查詢：Operation | where Detail has 'OverQuota'
   - 依據：結果數目
   - 條件：大於
   - 閾值：0
   - 週期：5 (分鐘)
   - 頻率︰5 (分鐘)
- 警示規則名稱：已達到每日資料限制
- 嚴重性：警告 (Sev 1)

一旦定義警示且達到限制後，警示就會觸發，並執行動作群組中定義的回應。 其可以透過電子郵件和文字簡訊通知您的小組，或使用 Webhook、自動化 Runbook 或[與外部 ITSM 方案整合](itsmc-overview.md#create-itsm-work-items-from-azure-alerts)來自動採取動作。 

## <a name="troubleshooting-why-usage-is-higher-than-expected"></a>針對使用量高於預期的原因進行疑難排解

較高的使用量是由下列一個或兩個原因所造成：
- 將資料發送到日誌分析工作區的節點比預期多
- 發送到 Log Analytics 工作區的資料比預期多（可能是由於開始使用新解決方案或對現有解決方案的配置更改）

## <a name="understanding-nodes-sending-data"></a>瞭解發送資料的節點

要瞭解上個月每天從代理報告活動訊號的節點數，請使用

```kusto
Heartbeat 
| where TimeGenerated > startofday(ago(31d))
| summarize nodes = dcount(Computer) by bin(TimeGenerated, 1d)    
| render timechart
```
獲取過去 24 小時內發送資料的節點計數使用查詢： 

```kusto
union withsource = tt * 
| where TimeGenerated > ago(24h)
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize nodes = dcount(computerName)
```

要獲取發送任何資料的節點清單（以及每個資料發送的資料量），可以使用以下查詢：

```kusto
union withsource = tt * 
| where TimeGenerated > ago(24h)
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize TotalVolumeBytes=sum(_BilledSize) by computerName
```

> [!NOTE]
> 請謹慎使用這些 `union withsource = tt *` 查詢，因為執行跨資料類型掃描的費用相當高昂。 此查詢將查詢每台電腦資訊的舊方法替換為"使用方式"資料類型。  

## <a name="understanding-ingested-data-volume"></a>瞭解引入的資料量

在 [使用量和估計成本]**** 頁面上，[每個解決方案的資料擷取]** 圖表會顯示所傳送的資料總量，以及每個解決方案所傳送的資料量。 這可讓您判斷各種趨勢，例如整體資料使用量 (或特定解決方案的使用量) 是否正在增長、穩定不變或正在減少。 

### <a name="data-volume-for-specific-events"></a>特定事件的資料量

要查看特定事件集的引入資料的大小，可以查詢特定表（在此示例中`Event`），然後將查詢限制為感興趣的事件（在此示例中為 ID 5145 或 5156）：

```kusto
Event
| where TimeGenerated > startofday(ago(31d)) and TimeGenerated < startofday(now()) 
| where EventID == 5145 or EventID == 5156
| where _IsBillable == true
| summarize count(), Bytes=sum(_BilledSize) by EventID, bin(TimeGenerated, 1d)
``` 

請注意，子句`where IsBillable = true`從某些沒有引入費用的解決方案中篩選出資料類型。 

### <a name="data-volume-by-solution"></a>依方案分類的資料量

用於查看上個月（不包括最後一個部分）的計費資料量的查詢是：

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by bin(StartTime, 1d), Solution | render barchart
```

中的`TimeGenerated`子句僅旨在確保 Azure 門戶中的查詢體驗將回顧預設 24 小時之外。 使用"使用方式"資料類型時，`StartTime`並`EndTime`表示顯示結果的時間桶。 

### <a name="data-volume-by-type"></a>按類型進行的資料量

您可以進一步鑽取以查看資料類型的資料趨勢：

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by bin(StartTime, 1d), DataType | render barchart
```

或者查看按解決方案和上個月類型的表，

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) by Solution, DataType
| sort by Solution asc, DataType asc
```

### <a name="data-volume-by-computer"></a>按電腦進行的資料量

資料類型`Usage`不包括完整級別的資訊。 要查看每台電腦引入的資料**的大小**，`_BilledSize`請使用 屬性 ，該[屬性](log-standard-properties.md#_billedsize)以位元組為單位提供大小：

```kusto
union withsource = tt * 
| where TimeGenerated > ago(24h)
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize BillableDataBytes = sum(_BilledSize) by  computerName | sort by Bytes nulls last
```

屬性`_IsBillable`[指定](log-standard-properties.md#_isbillable)引入的資料是否會產生費用。

要查看每台電腦引入的計費事件**計數**，請使用 

```kusto
union withsource = tt * 
| where TimeGenerated > ago(24h)
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize eventCount = count() by computerName  | sort by eventCount nulls last
```

### <a name="data-volume-by-azure-resource-resource-group-or-subscription"></a>按 Azure 資源、資源組或訂閱進行的資料卷

對於 Azure 中託管節點中的資料，可以獲取__每台電腦__的引入資料**的大小**，請使用_ResourceId屬性 ，該[屬性](log-standard-properties.md#_resourceid)提供資源的完整路徑：

```kusto
union withsource = tt * 
| where TimeGenerated > ago(24h)
| where _IsBillable == true 
| summarize BillableDataBytes = sum(_BilledSize) by _ResourceId | sort by Bytes nulls last
```

對於 Azure 中託管節點中的資料，可以__獲取每個 Azure 訂閱__的引入資料`_ResourceId`**的大小**，將屬性分析為：

```kusto
union withsource = tt * 
| where TimeGenerated > ago(24h)
| where _IsBillable == true 
| parse tolower(_ResourceId) with "/subscriptions/" subscriptionId "/resourcegroups/" 
    resourceGroup "/providers/" provider "/" resourceType "/" resourceName   
| summarize BillableDataBytes = sum(_BilledSize) by subscriptionId | sort by Bytes nulls last
```

更改為`subscriptionId``resourceGroup`將按 Azure 資源組顯示計費引入的資料卷。 

> [!NOTE]
> [使用量] 資料類型的部分欄位雖然仍位於結構描述中，但皆已過時，且系統將不再填入其值。 這包括 [Computer]****，以及其他與擷取相關的欄位 ([TotalBatches]****、[BatchesWithinSla]****、[BatchesOutsideSla]****、[BatchesCapped]****，以及 [AverageProcessingTimeMs]****)。

### <a name="querying-for-common-data-types"></a>查詢常見資料類型

若要更深入挖掘特定資料類型的資料來源，以下是一些實用的範例查詢：

+ **安全性**解決方案
  - `SecurityEvent | summarize AggregatedValue = count() by EventID`
+ **記錄管理**解決方案
  - `Usage | where Solution == "LogManagement" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | summarize AggregatedValue = count() by DataType`
+ **Perf** 資料類型
  - `Perf | summarize AggregatedValue = count() by CounterPath`
  - `Perf | summarize AggregatedValue = count() by CounterName`
+ **Event** 資料類型
  - `Event | summarize AggregatedValue = count() by EventID`
  - `Event | summarize AggregatedValue = count() by EventLog, EventLevelName`
+ **Syslog** 資料類型
  - `Syslog | summarize AggregatedValue = count() by Facility, SeverityLevel`
  - `Syslog | summarize AggregatedValue = count() by ProcessName`
+ **AzureDiagnostics** 資料類型
  - `AzureDiagnostics | summarize AggregatedValue = count() by ResourceProvider, ResourceId`

### <a name="tips-for-reducing-data-volume"></a>降低成本的訣竅

降低所收集記錄量的一些建議包括：

| 高資料量的來源 | 如何縮減資料量 |
| -------------------------- | ------------------------- |
| 安全性事件            | 選取[一般或最小安全性事件](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection#data-collection-tier) <br> 變更安全性稽核原則為只收集所需事件。 特別檢閱下列原則是否需要收集事件： <br> - [a稽核篩選平台](https://technet.microsoft.com/library/dd772749(WS.10).aspx) <br> - [稽核登錄](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941614(v%3dws.10))<br> - [稽核檔案系統](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772661(v%3dws.10))<br> - [稽核核心物件](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941615(v%3dws.10))<br> - [稽核控制代碼操作](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772626(v%3dws.10))<br> - 稽核抽取式存放裝置 |
| 效能計數器       | 變更[效能計數器組態](data-sources-performance-counters.md)以： <br> - 減少收集頻率 <br> - 減少效能計數器的數目 |
| 事件記錄                 | 變更[事件記錄組態](data-sources-windows-events.md)以： <br> - 減少所收集的事件記錄數目 <br> - 只收集必要的事件層級。 例如，不要收集「資訊」** 層級事件 |
| syslog                     | 變更 [Syslog 組態](data-sources-syslog.md)以： <br> - 減少所收集的設施數目 <br> - 只收集必要的事件層級。 例如，不要收集「資訊」** 和「偵錯」** 層級事件 |
| AzureDiagnostics           | 變更資源記錄集合： <br> - 減少會將記錄傳送至 Log Analytics 的資源數目 <br> - 只收集必要的記錄 |
| 電腦中不需要解決方案的方案資料 | 使用[解決方案定位](../insights/solution-targeting.md)從所需的電腦群組收集資料。 |

### <a name="getting-nodes-as-billed-in-the-per-node-pricing-tier"></a>獲取按每個節點定價層計費的節點

要獲取將計費為節點的電腦清單（如果工作區位於舊版"每個節點"定價層中），請查看發送**計費資料類型**的節點（某些資料類型是免費的）。 為此，請使用`_IsBillable`[屬性](log-standard-properties.md#_isbillable)並使用完全限定功能變數名稱的最左側欄位。 這將返回每小時計費資料的電腦計數（即計算和計費節點的細微性）：

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize billableNodes=dcount(computerName) by bin(TimeGenerated, 1h) | sort by TimeGenerated asc
```

### <a name="getting-security-and-automation-node-counts"></a>獲取安全和自動化節點計數

如果您是處於「每節點 (OMS)」定價層，則系統會根據您使用的節點和解決方案數目來向您收取費用，您需支付費用的見解與分析節點數目將會顯示在 [使用量和估計成本]**** 頁面上的資料表中。  

若要查看不同安全性節點的數目，您可以使用此查詢：

```kusto
union
(
    Heartbeat
    | where (Solutions has 'security' or Solutions has 'antimalware' or Solutions has 'securitycenter')
    | project Computer
),
(
    ProtectionStatus
    | where Computer !in~
    (
        (
            Heartbeat
            | project Computer
        )
    )
    | project Computer
)
| distinct Computer
| project lowComputer = tolower(Computer)
| distinct lowComputer
| count
```

若要查看不同自動化節點的數目，請使用此查詢：

```kusto
 ConfigurationData 
 | where (ConfigDataType == "WindowsServices" or ConfigDataType == "Software" or ConfigDataType =="Daemons") 
 | extend lowComputer = tolower(Computer) | summarize by lowComputer 
 | join (
     Heartbeat 
       | where SCAgentChannel == "Direct"
       | extend lowComputer = tolower(Computer) | summarize by lowComputer, ComputerEnvironment
 ) on lowComputer
 | summarize count() by ComputerEnvironment | sort by ComputerEnvironment asc
```

## <a name="create-an-alert-when-data-collection-is-high"></a>在資料收集高度時創建警報

本節說明在下列情況下如何建立警示：
- 資料量超出指定的數量。
- 資料量預計會超出指定的數量。

Azure 警示支援使用搜尋查詢的[記錄警示](alerts-unified-log.md)。 

在過去 24 小時收集超過 100GB 的資料時，下列查詢的結果：

```kusto
union withsource = $table Usage 
| where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true 
| extend Type = $table | summarize DataGB = sum((Quantity / 1000.)) by Type 
| where DataGB > 100
```

下列查詢會使用簡單的公式來預測在一天中何時會傳送超過 100GB 的資料： 

```kusto
union withsource = $table Usage 
| where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true 
| extend Type = $table 
| summarize EstimatedGB = sum(((Quantity * 8) / 1000.)) by Type 
| where EstimatedGB > 100
```

若要針對不同的資料量顯示警示，請將查詢中的 100 變更為您要顯示警示的 GB 數。

使用[建立新記錄警示](alerts-metric.md)中所述的步驟，可在資料收集高於預期時收到通知。

建立第一個查詢的警示時 - 在 24 小時內有超過 100GB 的資料時，請：  

- **定義警示條件**：將您的 Log Analytics 工作區指定為資源目標。
- **警示準則**：指定下列項目：
   - **信號名稱**選擇**自訂日誌搜索**
   - 將 [搜尋查詢]**** 設定為 `union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize DataGB = sum((Quantity / 1000.)) by Type | where DataGB > 100`
   - [警示邏輯]**** 為 [根據結果數目]**** **，而 [條件]**** 為 [大於臨界值 0]********
   - [時間週期]**** 為 1440** 分鐘，而 [警示頻率]**** 設定為 60** 分鐘，因為使用量資料每小時只會更新一次。
- **定義警示詳細資料**：指定下列項目：
   - 將 [名稱]**** 設定為「在 24 小時內大於 100GB 的資料量」**
   - 將 [嚴重性]**** 設定為「警告」**

指定現有或建立新的[動作群組](action-groups.md)，以便在記錄警示符合準則時收到通知。

建立第二個查詢的警示時 - 預計在 24 小時內會有超過 100GB 的資料時，請：

- **定義警示條件**：將您的 Log Analytics 工作區指定為資源目標。
- **警示準則**：指定下列項目：
   - **信號名稱**選擇**自訂日誌搜索**
   - 將 [搜尋查詢]**** 設定為 `union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize EstimatedGB = sum(((Quantity * 8) / 1000.)) by Type | where EstimatedGB > 100`
   - [警示邏輯]**** 為 [根據結果數目]**** **，而 [條件]**** 為 [大於臨界值 0]********
   - [時間週期]**** 為 180** 分鐘，而 [警示頻率]**** 設定為 60** 分鐘，因為使用量資料每小時只會更新一次。
- **定義警示詳細資料**：指定下列項目：
   - 將 [名稱]**** 設定為「預計在 24 小時內大於 100GB 的資料量」**
   - 將 [嚴重性]**** 設定為「警告」**

指定現有或建立新的[動作群組](action-groups.md)，以便在記錄警示符合準則時收到通知。

當您收到警示時，請使用下一節中的步驟，針對使用量高於預期的原因進行疑難排解。

## <a name="data-transfer-charges-using-log-analytics"></a>使用日誌分析的資料傳輸費用

將資料發送到日誌分析可能會產生資料頻寬費用。 如 Azure[頻寬定價頁](https://azure.microsoft.com/pricing/details/bandwidth/)中所述，位於兩個區域中的 Azure 服務之間的資料傳輸按正常速率作為出站資料傳輸。 入站資料傳輸是免費的。 但是，此費用非常小（很少%）與日誌分析資料引入的成本相比。 因此，控制日誌分析的成本需要專注于您的引入資料量，我們有指導來説明[理解這一點](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-ingested-data-volume)。   


## <a name="troubleshooting-why-log-analytics-is-no-longer-collecting-data"></a>針對 Log Analytics 為什麼不再收集資料的問題進行疑難排解

如果您處於舊版「免費」定價層並在某日傳送了超過 500 MB 的資料，就會停止收集當日的其餘資料。 達到每日限制是 Log Analytics 停止收集資料或資料似乎遺失的常見原因。  當資料收集開始及停止時，Log Analytics 會建立 Operation 類型的事件。 在搜尋中執行下列查詢，即可檢查您是否達到每日限制並遺失資料： 

```kusto
Operation | where OperationCategory == 'Data Collection Status'
```

當資料收集停止時，OperationStatus 為 **Warning**。 當資料收集開始時，OperationStatus 為 **Succeeded**。 下表描述資料收集停止的原因，並建議為繼續資料收集所要採取的動作：  

|收集停止的原因| 解決方法| 
|-----------------------|---------|
|已達舊版免費定價層的每日限制 |請等到隔天自動重新開始收集，或變更為付費定價層。|
|已達您工作區的每日上限|等到自動重新開始收集或提高每日資料量限制，如「管理每日資料量上限」中所述。 每日上限重設時間會顯示於 [資料量管理]**** 頁面上。 |
|Azure 訂用帳戶處於暫停狀態，原因如下：<br> 免費試用已結束<br> Azure Pass 已過期<br> 已達每月消費限制 (例如 MSDN 或 Visual Studio 訂閱)|轉換成付費訂閱<br> 移除限制，或等到限制重設|

要在資料收集停止時收到通知，請使用 *"創建每日資料上限警報"* 中描述的步驟，在資料收集停止時收到通知。 使用[創建操作組](action-groups.md)中描述的步驟為警報規則配置電子郵件、Webhook 或 Runbook 操作。 

## <a name="limits-summary"></a>限制摘要

還有其他一些日誌分析限制，其中一些限制取決於日誌分析定價層。 這些記錄[在這裡](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#log-analytics-workspaces)。


## <a name="next-steps"></a>後續步驟

- 請參閱[Azure 監視器日誌中的日誌搜索](../log-query/log-query-overview.md)，瞭解如何使用搜索語言。 您可以使用搜尋查詢，對使用量資料執行額外的分析。
- 使用[建立新的記錄警示](alerts-metric.md)中所述的步驟，可在符合搜尋條件時收到通知。
- 使用[解決方案定位](../insights/solution-targeting.md)從所需的電腦群組收集資料。
- 若要設定有效的事件收集原則，請檢閱 [Azure 資訊安全中心篩選原則](../../security-center/security-center-enable-data-collection.md)。
- 更改[效能計數器配置](data-sources-performance-counters.md)。
- 要修改事件集合設置，請查看[事件日誌配置](data-sources-windows-events.md)。
- 要修改 syslog 集合設置，請查看[syslog 配置](data-sources-syslog.md)。