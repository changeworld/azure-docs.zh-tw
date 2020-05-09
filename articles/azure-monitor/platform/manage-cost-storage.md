---
title: 管理 Azure 監視器記錄的使用量和成本
description: 瞭解如何在 Azure 監視器中變更 Log Analytics 工作區的定價方案和管理資料量和保留原則。
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
ms.date: 05/07/2020
ms.author: bwren
ms.subservice: ''
ms.openlocfilehash: a2df89bc18ea5d0098ac5ebb0bc06b9df6728705
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/09/2020
ms.locfileid: "82993752"
---
# <a name="manage-usage-and-costs-with-azure-monitor-logs"></a>使用 Azure 監視器記錄來管理使用量和成本

> [!NOTE]
> 本文說明如何瞭解並控制 Azure 監視器記錄的成本。 相關文章、[監視使用量和估計成本](https://docs.microsoft.com/azure/azure-monitor/platform/usage-estimated-costs)說明如何針對不同的計價模式，跨多項 Azure 監視功能來查看使用量和估計成本。 本文所顯示的所有價格和成本僅供範例之用。 

Azure 監視器記錄是設計用來調整和支援每天從企業中的任何來源收集、編制索引和儲存大量資料，或部署在 Azure 中。  雖然這可能是您組織的主要推動力，但成本效率終究是最基本的推動力。 為此，請務必瞭解 Log Analytics 工作區的成本不是根據所收集的資料量，也取決於選取的方案，以及您選擇儲存從連線來源產生之資料的時間長度。  

在本文中，我們將探討如何主動監視內嵌資料量和儲存體成長，並定義限制以控制這些相關成本。 

## <a name="pricing-model"></a>定價模式

Log Analytics 的預設定價是以資料量內嵌為基礎的隨**用隨付**模型，並選擇性地保留較長的資料保留。 資料量會以要儲存的資料大小來測量。 每個 Log Analytics 工作區都是以個別服務的方式計費，並會提供給您的 Azure 訂用帳戶帳單。 視下列因素而定，資料內嵌量可能會相當可觀： 

  - 已啟用的管理解決方案數目和其設定
  - 受監視的 Vm 數目
  - 從每個受監視的 VM 收集的資料類型 
  
除了隨用隨付模型以外，Log Analytics 還有**容量保留**層，可讓您省下25% 的費用（相較于隨用隨付價格）。 容量保留定價可讓您從 100 GB/天開始購買保留。 任何高於保留層級的使用量都會依照隨用隨付費率計費。 容量保留層有31天的承諾期間。 在承諾期間，您可以變更為較高層級的容量保留層（這會重新開機31天的承諾用量期間），但在承諾期限完成之前，您無法移回「預付型方案」或「較低的容量保留層」。 容量保留層的計費會每日執行。 [深入瞭解](https://azure.microsoft.com/pricing/details/monitor/)Log Analytics 的隨用隨付和容量保留定價。 

在所有定價層中，資料磁片區會從資料的字串標記法計算出來，因為它已準備好要儲存。 [所有資料類型通用](https://docs.microsoft.com/azure/azure-monitor/platform/log-standard-properties)的數個屬性都不會包含在事件大小的計算中， `_ResourceId`包括`_ItemId`、 `_IsBillable`和`_BilledSize`。

另請注意，某些解決方案（例如[Azure 資訊安全中心](https://azure.microsoft.com/pricing/details/security-center/)、 [Azure Sentinel](https://azure.microsoft.com/pricing/details/azure-sentinel/)和設定[管理](https://azure.microsoft.com/pricing/details/automation/)）有自己的計價模式。 

### <a name="log-analytics-clusters"></a>Log Analytics 叢集

Log Analytics 叢集是工作區的集合，屬於單一受控 Azure 資料總管叢集，以支援[客戶管理的金鑰](https://docs.microsoft.com/azure/azure-monitor/platform/customer-managed-keys)之類的先進案例。  相較于隨用隨付定價，Log Analytics 叢集僅支援從 1000 GB/天開始的容量保留定價模型，並享有25% 的折扣。 任何高於保留層級的使用量都會依照隨用隨付費率計費。 在保留層級增加後，叢集容量保留期會有31天的承諾期間。 在承諾期間，無法減少容量保留層級，但可以隨時增加。 深入瞭解如何[建立 Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/customer-managed-keys#create-cluster-resource)叢集並[將工作區關聯至該](https://docs.microsoft.com/azure/azure-monitor/platform/customer-managed-keys#workspace-association-to-cluster-resource)叢集。  

叢集容量保留層級是透過使用中的`Capacity`參數，透過以程式設計`Sku`方式使用 Azure Resource Manager 進行設定。 `Capacity`是以 GB 為單位來指定，而且每天的值可以是 1000 gb/天或更多（以每日 100 GB 為增量）。 [這裡](https://docs.microsoft.com/azure/azure-monitor/platform/customer-managed-keys#create-cluster-resource)有詳細說明。 如果您的叢集需要每日超過 2000 GB 的保留，請[LAIngestionRate@microsoft.com](mailto:LAIngestionRate@microsoft.com)聯絡我們。

因為內嵌資料的計費是在叢集層級完成，所以與叢集相關聯的工作區不再具有定價層。 會匯總與叢集相關聯的每個工作區中的內嵌資料數量，以計算叢集的每日帳單。 請注意， [Azure 資訊安全中心](https://docs.microsoft.com/azure/security-center/)的個別節點配置會在此工作區層級套用，這會在叢集中所有工作區的匯總資料匯總之前進行。 資料保留期仍會以工作區層級計費。 請注意，叢集計費會在建立叢集時啟動，無論工作區是否已與叢集相關聯。 

## <a name="estimating-the-costs-to-manage-your-environment"></a>估計管理環境的成本 

如果您尚未使用 Azure 監視器記錄，您可以使用[Azure 監視器定價計算機](https://azure.microsoft.com/pricing/calculator/?service=monitor)來估計使用 Log Analytics 的成本。 一開始請在搜尋方塊中輸入 "Azure 監視器"，然後按一下產生的 [Azure 監視器] 磚。 向下展開頁面以 Azure 監視器，然後從 [類型] 下拉式清單中選取 [Log Analytics]。  您可以在這裡輸入 Vm 的數目，以及您預期要從每個 VM 收集的 GB 資料。 通常是從一般 Azure VM 內嵌1到 3 GB 的資料月。 如果您已經在評估 Azure 監視器的記錄，您可以從自己的環境使用您的資料統計資料。 請參閱下文，以瞭解如何判斷[受監視的 vm 數目](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-nodes-sending-data)，以及[您的工作區所內嵌的資料量](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-ingested-data-volume)。 

## <a name="understand-your-usage-and-estimate-costs"></a>瞭解您的使用量和估計成本

如果您目前使用 Azure 監視器記錄，很容易就能根據最近的使用模式來瞭解可能的成本。 若要這麼做，請使用**Log Analytics 使用量和估計成本**來審查和分析資料使用量。 這會顯示每個解決方案收集的資料量、保留的資料量，以及根據資料內嵌數量和超出所含數量的任何額外保留來估計成本。

![使用量和估計成本](media/manage-cost-storage/usage-estimated-cost-dashboard-01.png)

若要更詳細地探索您的資料，請在 [使用量和估計成本]**** 頁面上，按一下位於任一圖表右上方的圖示。 現在您可以使用此查詢來探索更詳細的使用量。  

![記錄檢視](media/manage-cost-storage/logs.png)

您可以從 [使用量和估計成本]**** 頁面檢閱當月的資料量。 這之中包括 Log Analytics 工作區中接收和保留的所有資料。  按一下頁面頂端的 [**使用量詳細資料**]，以依據來源、電腦和供應專案，使用資料量趨勢的資訊來查看使用量儀表板。 若要檢視和設定每日上限或修改保留期限，請按一下 [資料量管理]****。
 
Log Analytics 費用會新增到您的 Azure 帳單中。 您可以在 Azure 入口網站中的 [帳務] 區段下，或在 [Azure 計費入口網站](https://account.windowsazure.com/Subscriptions)中，查看 Azure 帳單的詳細資料。  

## <a name="viewing-log-analytics-usage-on-your-azure-bill"></a>在您的 Azure 帳單上查看 Log Analytics 使用量 

Azure 在[Azure 成本管理 + 計費](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis?toc=/azure/billing/TOC.json)中樞提供了大量有用的功能。 例如，「成本分析」功能可讓您查看 Azure 資源的花費。 首先，依「資源類型」（適用于 log Analytics 的 microsoft.operationalinsights/工作區和 Log Analytics 叢集的 microsoft.operationalinsights/工作區）新增篩選器，可讓您追蹤 Log Analytics 的支出。 然後針對 [群組依據] 選取 [計量類別目錄] 或 [計量]。  請注意，Azure 資訊安全中心和 Azure Sentinel 等其他服務也會對 Log Analytics 工作區資源收取其使用量的費用。 若要查看服務名稱的對應，您可以選取 [資料表] 視圖，而不是 [圖表]。 

[從 Azure 入口網站下載您的使用量](https://docs.microsoft.com/azure/billing/billing-download-azure-invoice-daily-usage-date#download-usage-in-azure-portal)，即可取得更多使用方式的瞭解。 在下載的試算表中，您可以看到每天每個 Azure 資源（例如 Log Analytics 工作區）的使用量。 在此 Excel 試算表中，您可以藉由在 [計量類別] 資料行上先篩選以顯示「Log Analytics」，找到 Log Analytics 工作區的使用量。深入解析與分析」（由一些舊版定價層使用）和 "Azure 監視器" （由容量保留定價層使用），然後在「實例識別碼」資料行上新增篩選，其為「包含工作區」或「包含叢集」（後者包含 Log Analytics 叢集使用方式）。 使用量會顯示在 [取用的數量] 資料行中，每個專案的單位會顯示在 [測量單位] 資料行中。  有更多詳細資料可協助您[瞭解您的 Microsoft Azure 帳單](https://docs.microsoft.com/azure/billing/billing-understand-your-bill)。 

## <a name="changing-pricing-tier"></a>正在變更定價層

若要變更工作區的 Log Analytics 定價層， 

1. 在 [Azure 入口網站中，從您的工作區開啟 [**使用量] 和 [估計成本**]，您會在其中看到此工作區可用的每個定價層清單。

2. 檢查每個定價層的預估成本。 這項預估是以過去31天的使用量為基礎，因此此成本預估會依賴過去31天來代表您的一般使用方式。 在下列範例中，您可以看到根據過去31天的資料模式，此工作區在隨用隨付層（#1）中的成本會比 100 GB/天容量保留層（#2）少。  

    ![定價層](media/manage-cost-storage/pricing-tier-estimated-costs.png)

3. 根據過去31天的使用量來審查估計成本之後，如果您決定變更定價層，請按一下 [**選取**]。  

您也可以使用`sku`參數（`pricingTier`在 Azure Resource Manager 範本中），透過 Azure Resource Manager 來[設定定價層](https://docs.microsoft.com/azure/azure-monitor/platform/template-workspace-configuration#configure-a-log-analytics-workspace)。 

## <a name="legacy-pricing-tiers"></a>舊版定價層

在2018年4月2日之前具有 Log Analytics 工作區或 Application Insights 資源的訂用帳戶，或連結至2019年2月1日前啟動的 Enterprise 合約，將可繼續存取使用舊版定價層：**免費**、**獨立（每 GB）** 和**每節點（OMS）**。  免費定價層中的工作區會將每日資料內嵌限制為 500 MB （但 Azure 資訊安全中心所收集的安全性資料類型除外），而且資料保留期僅限7天。 免費定價層僅供評估之用。 獨立或每個節點定價層中的工作區，可讓使用者設定保留30到730天。

每個節點的定價層每個受監視 VM （節點）的費用（以小時為單位）。 針對每個受監視的節點，工作區會每日配置 500 MB 的資料，而不計費。 此配置會在工作區層級進行匯總。 高於匯總每日資料配置的資料內嵌會依資料超額數的 GB 計費。 請注意，如果工作區是在每個節點的定價層中，則在您的帳單上，服務將會是 Log Analytics 使用量的**深入解析和分析**。 

> [!TIP]
> 如果您的工作區具有**每個節點**定價層的存取權，但您想要知道它在隨用隨付層中是否成本較低，您可以[使用下列查詢](#evaluating-the-legacy-per-node-pricing-tier)來輕鬆取得建議。 

在2016年4月之前建立的工作區也可以存取原始**標準**和**Premium**定價層，分別保留30和365天的固定資料。 無法在**標準****或高階**定價層中建立新的工作區，而且如果工作區移出這些層級，就無法將其移回。 

如需定價層限制的詳細資料，請參閱[這裡](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#log-analytics-workspaces)。

> [!NOTE]
> 若要使用來自購買 OMS E1套件、OMS E2 套件或 OMS Add-On for System Center 的權利，請選擇 Log Analytics [每個節點]** 定價層。

## <a name="change-the-data-retention-period"></a>變更資料保留期

下列步驟說明如何設定您工作區中的記錄資料保留時間。 除非所有工作區使用舊版免費定價層，否則資料保留期可以設定為30到730天（2年）。[深入瞭解](https://azure.microsoft.com/pricing/details/monitor/)較長資料保留的定價。 

### <a name="default-retention"></a>預設保留期

若要設定工作區的預設保留期， 
 
1. 在 [Azure 入口網站] 的工作區中，從左窗格中選取 [**使用量] 和 [估計成本**]。
2. 在 [使用量和估計成本]**** 頁面上，按一下頁面頂端的 [資料量管理]****。
3. 在窗格上，移動滑桿來增加或減少天數，然後按一下 [確定]****。  如果您位於「免費」** 層，將無法修改資料保留期，必須升級至付費層，才能控制此設定。

    ![變更工作區資料保留期設定](media/manage-cost-storage/manage-cost-change-retention-01.png)

降低保留期時，會在移除最舊的資料之前，有幾天的寬限期。 
    
您也可以使用`retentionInDays`參數，透過 Azure Resource Manager 來[設定](https://docs.microsoft.com/azure/azure-monitor/platform/template-workspace-configuration#configure-a-log-analytics-workspace)保留期。 此外，如果您將資料保留期設定為30天，您可以使用`immediatePurgeDataOn30Days`參數觸發立即清除較舊的資料，這可能適用于合規性相關案例。 此功能只會透過 Azure Resource Manager 公開。 

兩個資料類型- `Usage` - `AzureActivity`和--預設會保留90天，而此90天保留期免費。 這些資料類型也是資料內嵌費用的免費。 



### <a name="retention-by-data-type"></a>依資料類型的保留期

您也可以為個別資料類型指定不同的保留設定，包括30到730天（舊版免費定價層中的工作區除外）。 每種資料類型都是工作區的子資源。 例如，SecurityEvent 資料表可以在[Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)中定址，如下所示：

```
/subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent
```

請注意，資料類型（資料表）會區分大小寫。  若要取得特定資料類型的目前每個資料類型保留設定（在此範例中為 SecurityEvent），請使用：

```JSON
    GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview
```

若要取得工作區中所有資料類型的目前每個資料類型保留設定，只需省略特定的資料類型，例如：

```JSON
    GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables?api-version=2017-04-26-preview
```

若要將特定資料類型的保留（在此範例中為 SecurityEvent）設定為730天，請執行

```JSON
    PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview
    {
        "properties": 
        {
            "retentionInDays": 730
        }
    }
```

的有效值為`retentionInDays`從30到730。

`Usage`和`AzureActivity`資料類型不能使用自訂保留來設定。 它們會採用預設工作區保留期或90天的最大值。 

[OSS] 工具[ARMclient](https://github.com/projectkudu/ARMClient)是直接連接到 Azure Resource Manager 以根據資料類型來設定保留的絕佳工具。  深入瞭解[David Ebbo](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html)和[Daniel Bowbyes](https://blog.bowbyes.co.nz/2016/11/02/using-armclient-to-directly-access-azure-arm-rest-apis-and-list-arm-policy-details/)的文章 ARMclient。  以下是使用 ARMClient 的範例，將 SecurityEvent 資料設定為730天保留期：

```
armclient PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview "{properties: {retentionInDays: 730}}"
```

> [!TIP]
> 您可以使用個別資料類型的設定保留來降低資料保留成本。  針對從2019年10月（發行這項功能時）開始收集的資料，減少某些資料類型的保留期，可以降低您一段時間的保留成本。  針對稍早收集的資料，針對個別類型設定較低的保留不會影響您的保留成本。  

## <a name="manage-your-maximum-daily-data-volume"></a>管理您的每日最大資料量

您可以為工作區設定每日上限和限制每日擷取，但請謹慎使用，因為您的目標是不要達到每日限制。  否則，您會遺失當天其餘時間的資料，這可能會影響其功能取決於工作區中一直在提供的最新資料的其他 Azure 服務和解決方案。  如此一來，您在資源健全狀況支援 IT 服務時觀察和接收警示的功能會受到影響。  每日上限是用來管理受控資源中非預期增加的資料量，並保持在限制範圍內，或當您想要限制工作區未規劃的費用時使用。  

一旦達到每日限制，計費資料類型的集合就會在當天的剩餘時間停止。 （套用每日上限時的固有延遲可能表示上限不會精確地套用到指定的每日上限層級）。在選取的 Log Analytics 工作區頁面頂端會出現警告橫幅，且作業事件會傳送至 [ **LogManagement** ] 類別下的 [ *operation* ] 資料表。 一旦過了「每日限制的設定時間」** 下定義的重設時間後，資料收集就會繼續執行。 我們建議您根據此作業事件定義警示規則，設定為在達到每日資料限制時發出通知。 

> [!WARNING]
> 每日上限不會從 Azure 資訊安全中心停止收集資料，但在2017年6月19日前安裝 Azure 資訊安全中心的工作區除外。 

### <a name="identify-what-daily-data-limit-to-define"></a>識別要定義的每日資料限制

檢閱 [Log Analytics 使用量和估計成本](usage-estimated-costs.md)，以了解資料擷取趨勢及要定義的每日資料量上限。 請謹慎考慮，因為您會在達到限制之後，才能夠監視您的資源。 

### <a name="set-the-daily-cap"></a>設定每日上限

下列步驟說明如何設定限制，以管理 Log Analytics 工作區每天將內嵌的資料量。  

1. 在工作區中，從左側窗格中選取 [使用量和估計成本]****。
2. 在所選工作區的 [使用量和估計成本]**** 頁面上，按一下頁面頂端的 [資料量管理]****。 
3. 預設會**關閉**每日上限？ 按一下 [**開啟**] 加以啟用，然後設定資料量限制（以 GB/天為單位）。

    ![Log Analytics 設定資料限制](media/manage-cost-storage/set-daily-volume-cap-01.png)

### <a name="alert-when-daily-cap-reached"></a>達到每日上限時發出警示

雖然我們會在您達到資料限制閾值時，於 Azure 入口網站中顯示視覺提示，但對於需要立刻處理的作業問題，此行為並不一定與您的管理方式一致。  若要接收警示通知，您可以在 Azure 監視器中建立新的警示規則。  若要深入瞭解，請參閱[如何建立、查看和管理警示](alerts-metric.md)。

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
- 超過預期將資料傳送至 Log Analytics 工作區的節點數
- 超過預期會傳送到 Log Analytics 工作區的資料（可能是因為開始使用新的解決方案或設定變更到現有的解決方案）

## <a name="understanding-nodes-sending-data"></a>瞭解傳送資料的節點

若要瞭解上個月每天從代理程式報告心跳的節點數目，請使用

```kusto
Heartbeat 
| where TimeGenerated > startofday(ago(31d))
| summarize nodes = dcount(Computer) by bin(TimeGenerated, 1d)    
| render timechart
```
[取得過去24小時內傳送資料的節點計數] 會使用查詢： 

```kusto
union withsource = tt * 
| where TimeGenerated > ago(24h)
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize nodes = dcount(computerName)
```

若要取得傳送任何資料的節點清單（以及每個的資料傳輸量），可以使用下列查詢：

```kusto
union withsource = tt * 
| where TimeGenerated > ago(24h)
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize TotalVolumeBytes=sum(_BilledSize) by computerName
```

> [!TIP]
> 請謹慎`union *`使用這些查詢，因為跨資料類型的掃描會[耗用大量資源](https://docs.microsoft.com/azure/azure-monitor/log-query/query-optimization#query-performance-pane)來執行。 如果您不需要每一部**電腦**的結果，請查詢 Usage 資料類型（請參閱下文）。

## <a name="understanding-ingested-data-volume"></a>瞭解內嵌資料量

在 [使用量和估計成本]**** 頁面上，[每個解決方案的資料擷取]** 圖表會顯示所傳送的資料總量，以及每個解決方案所傳送的資料量。 這可讓您判斷各種趨勢，例如整體資料使用量 (或特定解決方案的使用量) 是否正在增長、穩定不變或正在減少。 

### <a name="data-volume-for-specific-events"></a>特定事件的資料磁片區

若要查看特定事件集的內嵌資料大小，您可以查詢特定資料表（在此範例`Event`中為），然後將查詢限制為感興趣的事件（在此範例中為事件識別碼5145或5156）：

```kusto
Event
| where TimeGenerated > startofday(ago(31d)) and TimeGenerated < startofday(now()) 
| where EventID == 5145 or EventID == 5156
| where _IsBillable == true
| summarize count(), Bytes=sum(_BilledSize) by EventID, bin(TimeGenerated, 1d)
``` 

請注意，子句`where IsBillable = true`會從沒有任何內嵌費用的特定解決方案中篩選出資料類型。 

### <a name="data-volume-by-solution"></a>依方案分類的資料量

過去一個月（不包括最後一天），用來依解決方案來查看計費資料量的查詢如下：

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by bin(StartTime, 1d), Solution | render barchart
```

具有`TimeGenerated`的子句，只是為了確保 Azure 入口網站中的查詢體驗會看起來超過預設的24小時。 使用 Usage 資料類型時， `StartTime`和`EndTime`代表顯示結果的時間值區。 

### <a name="data-volume-by-type"></a>資料量（依類型）

您可以進一步向下切入，以查看依資料類型的資料趨勢：

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by bin(StartTime, 1d), DataType | render barchart
```

或者，若要依解決方案查看資料表，並輸入上個月的類型，

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) by Solution, DataType
| sort by Solution asc, DataType asc
```

### <a name="data-volume-by-computer"></a>資料量（依電腦）

`Usage`資料類型不包含電腦層級的資訊。 若要查看每部電腦的內嵌資料**大小**，請使用`_BilledSize` [屬性](log-standard-properties.md#_billedsize)，它會提供以位元組為單位的大小：

```kusto
union withsource = tt * 
| where TimeGenerated > ago(24h)
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize BillableDataBytes = sum(_BilledSize) by  computerName | sort by Bytes nulls last
```

`_IsBillable` [屬性](log-standard-properties.md#_isbillable)會指定內嵌資料是否會產生費用。 

若要查看每部電腦內嵌的可計費事件**計數**，請使用 

```kusto
union withsource = tt * 
| where TimeGenerated > ago(24h)
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize eventCount = count() by computerName  | sort by eventCount nulls last
```

> [!TIP]
> 請謹慎`union  *`使用這些查詢，因為跨資料類型的掃描會[耗用大量資源](https://docs.microsoft.com/azure/azure-monitor/log-query/query-optimization#query-performance-pane)來執行。 如果您不需要每一部**電腦**的結果，請查詢 Usage 資料類型。


### <a name="data-volume-by-azure-resource-resource-group-or-subscription"></a>依 Azure 資源、資源群組或訂用帳戶的資料量

針對 Azure 中裝載之節點的資料，您可以取得每一部__電腦__的內嵌資料**大小**，使用 _ResourceId[屬性](log-standard-properties.md#_resourceid)，它會提供資源的完整路徑：

```kusto
union withsource = tt * 
| where TimeGenerated > ago(24h)
| where _IsBillable == true 
| summarize BillableDataBytes = sum(_BilledSize) by _ResourceId | sort by Bytes nulls last
```

針對 Azure 中裝載之節點的資料，您可以取得__每個 azure 訂__用帳戶的內嵌資料`_ResourceId` **大小**，並將屬性剖析為：

```kusto
union withsource = tt * 
| where TimeGenerated > ago(24h)
| where _IsBillable == true 
| parse tolower(_ResourceId) with "/subscriptions/" subscriptionId "/resourcegroups/" 
    resourceGroup "/providers/" provider "/" resourceType "/" resourceName   
| summarize BillableDataBytes = sum(_BilledSize) by subscriptionId | sort by Bytes nulls last
```

將`subscriptionId`變更`resourceGroup`為會顯示 Azure 資源群組的可計費內嵌資料量。 

> [!TIP]
> 請謹慎`union  *`使用這些查詢，因為跨資料類型的掃描會[耗用大量資源](https://docs.microsoft.com/azure/azure-monitor/log-query/query-optimization#query-performance-pane)來執行。 如果您不需要每個訂用帳戶、資源群組或資源名稱的結果，請查詢 Usage 資料類型。

> [!WARNING]
> [使用量] 資料類型的部分欄位雖然仍位於結構描述中，但皆已過時，且系統將不再填入其值。 這包括 [Computer]****，以及其他與擷取相關的欄位 ([TotalBatches]****、[BatchesWithinSla]****、[BatchesOutsideSla]****、[BatchesCapped]****，以及 [AverageProcessingTimeMs]****)。

### <a name="querying-for-common-data-types"></a>查詢常見的資料類型

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
| 電腦中不需要解決方案的方案資料 | 使用[解決方案目標](../insights/solution-targeting.md)，只從必要的電腦群組收集資料。 |

### <a name="getting-nodes-as-billed-in-the-per-node-pricing-tier"></a>取得依每個節點定價層計費的節點

若要取得將計費為節點的電腦清單（如果工作區在舊版的每個節點定價層中），請尋找傳送**計費資料類型**的節點（某些資料類型是免費的）。 若要執行這項操作`_IsBillable` ，請使用[屬性](log-standard-properties.md#_isbillable)，並使用完整功能變數名稱的最左邊欄位。 這會傳回具有每小時計費資料的電腦計數（這是計算和計費節點的細微性）：

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize billableNodes=dcount(computerName) by bin(TimeGenerated, 1h) | sort by TimeGenerated asc
```

### <a name="getting-security-and-automation-node-counts"></a>取得安全性和自動化節點計數

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

## <a name="evaluating-the-legacy-per-node-pricing-tier"></a>評估舊版的每個節點定價層

在該階層中，或**在目前的預付型方案**或**容量保留**層中，有權存取具有舊版「**每節點**」定價層的工作區，通常很難進行評估。  這牽涉到了解每個節點定價層中每個受監視節點的固定成本與其內含的資料配置 500 MB/節點/天的取捨，以及只需支付預付型方案（每 GB）層中內嵌資料的成本。 

為了協助進行這項評估，您可以根據工作區的使用模式，使用下列查詢來提出最佳定價層的建議。  此查詢會查看過去7天內已監視的節點和資料內嵌至工作區，而且每一天會評估哪個定價層已達到最佳狀態。 若要使用查詢，您必須指定

1. 如果工作區是使用 Azure 資訊安全中心，請`workspaceHasSecurityCenter`將`true`設定`false`為或。 
2. 如果您有特定折扣，請更新價格，並
3. 指定要查看的天數，並依設定`daysToEvaluate`進行分析。 如果查詢花太長的時間嘗試查看7天的資料，這就很有用。 

以下是定價層建議查詢：

```kusto
// Set these parameters before running query
let workspaceHasSecurityCenter = true;  // Specify if the workspace has Azure Security Center
let PerNodePrice = 15.; // Enter your montly price per monitored nodes
let PerNodeOveragePrice = 2.30; // Enter your price per GB for data overage in the Per Node pricing tier
let PerGBPrice = 2.30; // Enter your price per GB in the Pay-as-you-go pricing tier
let daysToEvaluate = 7; // Enter number of previous days look at (reduce if the query is taking too long)
// ---------------------------------------
let SecurityDataTypes=dynamic(["SecurityAlert", "SecurityBaseline", "SecurityBaselineSummary", "SecurityDetection", "SecurityEvent", "WindowsFirewall", "MaliciousIPCommunication", "LinuxAuditLog", "SysmonEvent", "ProtectionStatus", "WindowsEvent", "Update", "UpdateSummary"]);
let StartDate = startofday(datetime_add("Day",-1*daysToEvaluate,now()));
let EndDate = startofday(now());
union withsource = tt * 
| where TimeGenerated >= StartDate and TimeGenerated < EndDate
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize nodesPerHour = dcount(computerName) by bin(TimeGenerated, 1h)  
| summarize nodesPerDay = sum(nodesPerHour)/24.  by day=bin(TimeGenerated, 1d)  
| join kind=leftouter (
    Heartbeat 
    | where TimeGenerated >= StartDate and TimeGenerated < EndDate
    | where Computer != ""
    | summarize ASCnodesPerHour = dcount(Computer) by bin(TimeGenerated, 1h) 
    | extend ASCnodesPerHour = iff(workspaceHasSecurityCenter, ASCnodesPerHour, 0)
    | summarize ASCnodesPerDay = sum(ASCnodesPerHour)/24.  by day=bin(TimeGenerated, 1d)   
) on day
| join (
    Usage 
    | where TimeGenerated >= StartDate and TimeGenerated < EndDate
    | where IsBillable == true
    | extend NonSecurityData = iff(DataType !in (SecurityDataTypes), Quantity, 0.)
    | extend SecurityData = iff(DataType in (SecurityDataTypes), Quantity, 0.)
    | summarize DataGB=sum(Quantity)/1000., NonSecurityDataGB=sum(NonSecurityData)/1000., SecurityDataGB=sum(SecurityData)/1000. by day=bin(StartTime, 1d)  
) on day
| extend AvgGbPerNode =  NonSecurityDataGB / nodesPerDay
| extend PerGBDailyCost = iff(workspaceHasSecurityCenter,
             (NonSecurityDataGB + max_of(SecurityDataGB - 0.5*ASCnodesPerDay, 0.)) * PerGBPrice,
             DataGB * PerGBPrice)
| extend OverageGB = iff(workspaceHasSecurityCenter, 
             max_of(DataGB - 0.5*nodesPerDay - 0.5*ASCnodesPerDay, 0.), 
             max_of(DataGB - 0.5*nodesPerDay, 0.))
| extend PerNodeDailyCost = nodesPerDay * PerNodePrice / 31. + OverageGB * PerNodeOveragePrice
| extend Recommendation = iff(PerNodeDailyCost < PerGBDailyCost, "Per Node tier", 
             iff(NonSecurityDataGB > 85., "Capacity Reservation tier", "Pay-as-you-go (Per GB) tier"))
| project day, nodesPerDay, ASCnodesPerDay, NonSecurityDataGB, SecurityDataGB, OverageGB, AvgGbPerNode, PerGBDailyCost, PerNodeDailyCost, Recommendation | sort by day asc
//| project day, Recommendation // Comment this line to see details
| sort by day asc
```

此查詢不是使用計算方式的確切複寫，但是在大部分情況下都能用來提供定價層建議。  

> [!NOTE]
> 若要使用來自購買 OMS E1套件、OMS E2 套件或 OMS Add-On for System Center 的權利，請選擇 Log Analytics [每個節點]** 定價層。

## <a name="create-an-alert-when-data-collection-is-high"></a>在資料收集偏高時建立警示

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
   - **信號名稱**選取**自訂記錄搜尋**
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
   - **信號名稱**選取**自訂記錄搜尋**
   - 將 [搜尋查詢]**** 設定為 `union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize EstimatedGB = sum(((Quantity * 8) / 1000.)) by Type | where EstimatedGB > 100`
   - [警示邏輯]**** 為 [根據結果數目]**** **，而 [條件]**** 為 [大於臨界值 0]********
   - [時間週期]**** 為 180** 分鐘，而 [警示頻率]**** 設定為 60** 分鐘，因為使用量資料每小時只會更新一次。
- **定義警示詳細資料**：指定下列項目：
   - 將 [名稱]**** 設定為「預計在 24 小時內大於 100GB 的資料量」**
   - 將 [嚴重性]**** 設定為「警告」**

指定現有或建立新的[動作群組](action-groups.md)，以便在記錄警示符合準則時收到通知。

當您收到警示時，請使用下一節中的步驟，針對使用量高於預期的原因進行疑難排解。

## <a name="data-transfer-charges-using-log-analytics"></a>使用 Log Analytics 的資料傳輸費用

將資料傳送至 Log Analytics 可能會產生資料頻寬費用。 如[Azure 頻寬定價頁面](https://azure.microsoft.com/pricing/details/bandwidth/)中所述，位於兩個區域的 Azure 服務之間的資料傳輸會依正常費率向輸出資料傳輸收費。 輸入資料傳輸是免費的。 不過，這種費用非常小（幾%）相較于 Log Analytics 資料內嵌的成本。 因此，控制 Log Analytics 的成本需要專注于[您的內嵌](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-ingested-data-volume)資料磁片區，而我們也有指導方針可協助您瞭解這點。   


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

若要在資料收集停止時收到通知，請使用「*建立每日資料上限*」警示中所述的步驟，以在資料收集停止時收到通知。 使用[建立動作群組](action-groups.md)中所述的步驟來設定警示規則的電子郵件、webhook 或 runbook 動作。 

## <a name="limits-summary"></a>限制摘要

還有一些額外的 Log Analytics 限制，其中有些會相依于 Log Analytics 定價層。 這些內容記載在[這裡](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#log-analytics-workspaces)。


## <a name="next-steps"></a>後續步驟

- 請參閱[Azure 監視器記錄檔中的記錄搜尋](../log-query/log-query-overview.md)，以瞭解如何使用搜尋語言。 您可以使用搜尋查詢，對使用量資料執行額外的分析。
- 使用[建立新的記錄警示](alerts-metric.md)中所述的步驟，可在符合搜尋條件時收到通知。
- 使用[解決方案目標](../insights/solution-targeting.md)，只從必要的電腦群組收集資料。
- 若要設定有效的事件收集原則，請檢閱 [Azure 資訊安全中心篩選原則](../../security-center/security-center-enable-data-collection.md)。
- 變更[效能計數器](data-sources-performance-counters.md)設定。
- 若要修改事件收集設定，請參閱[事件記錄](data-sources-windows-events.md)檔設定。
- 若要修改您的 syslog 集合設定，請參閱[syslog configuration](data-sources-syslog.md)。