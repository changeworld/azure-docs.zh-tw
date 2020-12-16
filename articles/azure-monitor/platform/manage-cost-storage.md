---
title: 管理 Azure 監視器記錄的使用量和成本
description: 了解如何在 Azure 監視器中變更 Log Analytics 工作區的定價方案，以及管理資料量和保留原則。
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
ms.date: 11/22/2020
ms.author: bwren
ms.subservice: ''
ms.openlocfilehash: a6b92d1b7f36b73d91b8e0e8e519981b936d8735
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/16/2020
ms.locfileid: "97592427"
---
# <a name="manage-usage-and-costs-with-azure-monitor-logs"></a>使用 Azure 監視器記錄來管理使用量和成本    

> [!NOTE]
> 本文說明如何了解和控制 Azure 監視器記錄的成本。 [監視使用量和估計成本](usage-estimated-costs.md)這篇相關文章會說明如何針對不同的定價模型，檢視多項 Azure 監視功能的使用量和估計成本。 本文所顯示的價格和成本全都只是作為範例。 

Azure 監視器記錄是為縮放及支援每日大量資料的收集、索引編製和儲存而設計，這些資料來源可能位於您企業內部或部署於 Azure 中。  雖然這可能是您組織的主要推動力，但成本效率終究是最基本的推動力。 為此，務必了解 Log Analytics 工作區的成本不只是以收集的資料量為基礎，也取決於選取的方案，以及您為連線來源所產生的資料選擇了多長的儲存時間。  

在本文中，我們會探討您可以如何主動監視所擷取的資料量和儲存體成長情況，並定義限制來控制這些相關成本。 

## <a name="pricing-model"></a>定價模式

Log Analytics 的預設定價是 **隨用隨付** 模型 (會以所擷取的資料量作為根據)，而且可選擇性地用於需要長時間保留資料的情況。 資料量會以 GB 儲存的資料大小來測量， (10 ^ 9 個位元組) 。 每項 Log Analytics 工作區都是個別計費的服務，並且會計入到您的 Azure 訂用帳戶帳單。 根據下列因素而定，所資料的擷取量可能會很可觀： 

  - 已啟用的管理解決方案數目和其設定
  - 已監視的 VM 數目
  - 從每個已監視 VM 收集來的資料類型 
  
除了隨用隨付模型外，Log Analytics 還有 **容量保留** 層，相較於隨用隨付價格，可讓您省下 25% 的費用。 容量保留定價可讓您從「100 GB/天」起購買保留。 高於保留層級的使用量則會以隨用隨付費率計費。 容量保留層有 31 天的承諾用量期間。 在承諾用量期間，您可以變更為較高層級的容量保留層 (這麼做會重新開始計算 31 天的承諾用量期間)，但在承諾用量期間結束之前，您都無法重新回到隨用隨付定價或較低的容量保留層。 容量保留層會每日計費。 [深入了解](https://azure.microsoft.com/pricing/details/monitor/) Log Analytics 的隨用隨付定價和容量保留定價。 

在所有的定價層中，事件的資料大小是從記錄檔分析中針對此事件儲存的屬性（不論是從代理程式傳送，還是在內嵌進程期間新增）的字串表示所計算。 這包括任何 [自訂欄位](custom-fields.md) ，這些欄位會在收集資料時新增，然後儲存在 Log Analytics 中。 所有資料類型通用的數個屬性，包括一些 [Log Analytics 標準屬性](./log-standard-columns.md)，都會在事件大小的計算中排除。 這包括 `_ResourceId` 、 `_ItemId` 、 `_IsBillable` `_BilledSize` 和 `Type` 。 儲存在 Log Analytics 中的其他所有屬性都會包含在事件大小的計算中。 某些資料類型完全免費，資料內嵌費用，例如 AzureActivity、心跳和使用類型。 若要判斷是否從資料內嵌的計費中排除某個事件，您可以使用 `_IsBillable` 屬性，如下[](#data-volume-for-specific-events)所示。 使用量會以 GB (1.0 E 9 bytes) 來回報。 

另請注意，某些解決方案 (例如 [Azure 資訊安全中心](https://azure.microsoft.com/pricing/details/security-center/)、[Azure Sentinel](https://azure.microsoft.com/pricing/details/azure-sentinel/) 和[設定管理](https://azure.microsoft.com/pricing/details/automation/)) 有自己的定價模型。 

### <a name="log-analytics-dedicated-clusters"></a>Log Analytics 專用叢集

Log Analytics 專用叢集會將各工作區集合到單一的受控 Azure 資料總管叢集，以支援[客戶管理的金鑰](customer-managed-keys.md)等進階案例。  Log Analytics 專用叢集使用的容量保留定價模型必須設定為至少 1000 GB/天。 相較于隨用隨付定價，此容量層級有25% 的折扣。 高於保留層級的使用量則會以隨用隨付費率計費。 在保留層級提高後，叢集的容量保留會有 31 天的承諾用量期間。 在承諾用量期間，您無法降低容量保留層級，但可以隨時提高。 當工作區與叢集相關聯時，會使用設定的容量保留層級，在叢集層級完成這些工作區的資料內嵌計費。 深入了解如何[建立 Log Analytics 叢集](customer-managed-keys.md#create-cluster)並[將工作區與其關聯](customer-managed-keys.md#link-workspace-to-cluster)。 容量保留定價資訊可在 [Azure 監視器定價] 頁面]( https://azure.microsoft.com/pricing/details/monitor/)取得。  

叢集容量保留層級是使用中的參數，以程式設計方式透過 Azure Resource Manager 來設定 `Capacity` `Sku` 。 `Capacity` 會以 GB 為單位來指定，而且可以有「1000 GB/天」以上、增量單位為「100 GB/天」的值。 這詳述于 [Azure 監視器客戶管理的金鑰](customer-managed-keys.md#create-cluster)。 如果您的叢集需要「2000 GB/天」以上的保留量，請透過以下信箱與我們連絡：[LAIngestionRate@microsoft.com](mailto:LAIngestionRate@microsoft.com)。

叢集上的使用量有兩種計費模式。 設定叢集時，參數可以指定這些 `billingType` 參數。 [](customer-managed-keys.md#customer-managed-key-operations) 兩種模式為： 

1. 叢集 **：在** 此案例中 (是預設) ，內嵌資料的計費是在叢集層級進行。 與叢集相關聯的每個工作區所擷取的資料數量會彙總起來，以計算叢集的每日帳單。 請注意，系統會先在工作區層級套用來自 [Azure 資訊安全中心](../../security-center/index.yml)的每一節點配置，然後才針對叢集中所有工作區的彙總資料進行此彙總。 

2. **工作區**：您叢集的容量保留成本會依叢集內的工作區進行比例化， (在會計入每個工作區 [Azure 資訊安全中心](../../security-center/index.yml) 的每個節點配置。 ) 如果內嵌到一天工作區的總數據量小於容量保留，則每個工作區的內嵌資料會以每 GB 容量保留費率計費，並藉由以容量保留的一部分計費來計費，而容量保留的未使用部分則會計費至叢集資源。 如果內嵌到一天工作區的總數據量超過容量保留量，則每個工作區會依據其內嵌資料的分數，以及每個工作區的容量保留比例，以每個工作區的比例計費。 如果每天內嵌到工作區中的資料量總計超過容量保留，則不會對叢集資源收費。

在叢集計費選項中，資料保留會依工作區計費。 請注意，當叢集建立好時，無論是否已有工作區與叢集關聯，叢集都會開始計費。 此外，請注意，與叢集相關聯的工作區不再具有定價層。

## <a name="estimating-the-costs-to-manage-your-environment"></a>估算環境管理成本 

如果您尚未使用 Azure 監視器記錄，則可以使用 [Azure 監視器定價計算機](https://azure.microsoft.com/pricing/calculator/?service=monitor)來估算 Log Analytics 的使用成本。 一開始請先在 [搜尋] 方塊中輸入「Azure 監視器」，然後按一下產生的 [Azure 監視器] 圖格。 將頁面向下捲動至 Azure 監視器，然後從 [類型] 下拉式清單中選取 [Log Analytics]。  您可以在這裡輸入 VM 的數目，以及您預期要從每個 VM 收集的資料 GB 數。 一般來說，典型的 Azure VM 每月會擷取 1 到 3 GB 的資料。 如果您已經在評估 Azure 監視器記錄，則可以使用自己環境中的資料統計值。 請參閱下文以了解如何判斷[已監視的 VM 數目](#understanding-nodes-sending-data)以及[您的工作區所擷取的資料量](#understanding-ingested-data-volume)。 

## <a name="understand-your-usage-and-estimate-costs"></a>了解您的使用量並估算成本

如果您目前有使用 Azure 監視器記錄，很容易就能根據最近的使用模式來了解大概的成本。 若要執行此動作，請使用 Log Analytics [使用量和估計成本] 來檢閱及分析資料使用量。 這能顯示每個解決方案所收集的資料量、所保留的資料量，以及根據所擷取的資料量及超出所包含量之任何額外保留資料量所進行的成本評估。

![使用量和估計成本](media/manage-cost-storage/usage-estimated-cost-dashboard-01.png)

若要更詳細地探索您的資料，請在 [使用量和估計成本] 頁面上，按一下位於任一圖表右上方的圖示。 現在您可以使用此查詢來探索更詳細的使用量。  

![記錄檢視](media/manage-cost-storage/logs.png)

您可以從 [使用量和估計成本] 頁面檢閱當月的資料量。 這包括您的 Log Analytics 工作區中收到和保留的所有計費資料。  
 
Log Analytics 費用會新增到您的 Azure 帳單中。 您可以在 Azure 入口網站中的 [帳務] 區段下，或在 [Azure 計費入口網站](https://account.windowsazure.com/Subscriptions)中，查看 Azure 帳單的詳細資料。  

## <a name="viewing-log-analytics-usage-on-your-azure-bill"></a>在 Azure 帳單上檢視 Log Analytics 使用量 

Azure 在 [Azure 成本管理 + 計費](../../cost-management-billing/costs/quick-acm-cost-analysis.md?toc=%2fazure%2fbilling%2fTOC.json)中樞內提供了大量實用功能。 例如，「成本分析」功能可讓您檢視 Azure 資源的花費。 首先，新增依「資源類型」的篩選器 (新增到 microsoft.operationalinsights/workspace for Log Analytics 和 microsoft.operationalinsights/workspace for Log Analytics Clusters) 可讓您追蹤 Log Analytics 的支出。 然後在 [群組依據] 中選取 [計量類別] 或 [計量]。  請注意，Azure 資訊安全中心和 Azure Sentinel 等其他服務也會對 Log Analytics 工作區資源收取使用費用。 若要查看服務名稱的對應，您可以選取 [資料表] 檢視而非圖表。 

若要更加了解您的使用量，請[從 Azure 入口網站下載使用量](../../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md#download-usage-in-azure-portal)。 在下載的試算表中，您可以看到每天每一 Azure 資源 (例如 Log Analytics 工作區) 的使用量。 在此 Excel 試算表中，您可以先在 [計量類別] 資料行進行篩選以顯示「Log Analytics」、「深入解析與分析」(某些舊版定價層會使用) 和「Azure 監視器」(容量保留定價層會使用)，然後在 [執行個體識別碼] 資料行上新增篩選 (「包含工作區」或「包含叢集」，後者可包含 Log Analytics 叢集使用量)，藉此找到 Log Analytics 工作區的使用量。 使用量會顯示在 [已取用的數量] 資料行，每個項目的單位則會顯示在 [測量單位] 資料行。  有更多詳細資料可協助您[了解 Microsoft Azure 帳單](../../cost-management-billing/understand/review-individual-bill.md)。 

## <a name="changing-pricing-tier"></a>正在變更定價層

若要變更工作區的 Log Analytics 定價層， 

1. 在 Azure 入口網站中，從您的工作區開啟 [使用量和估計成本]，以查看此工作區可用的每個定價層清單。

2. 檢閱每個定價層的估計成本。 這項估計會以過去 31 天的使用量為準，因此這項成本估計會以過去 31 天來代表您的一般使用量。 在下列範例中，您可以看到 (根據過去 31 天的資料模式) 此工作區在隨用隨付層 (#1) 的成本會比「100 GB/天」的容量保留層 (#2) 還低。  

    ![定價層](media/manage-cost-storage/pricing-tier-estimated-costs.png)

3. 在檢閱以過去 31 天的使用量為基礎的估計成本後，如果您決定變更定價層，請按一下 [選取]。  

您也可以使用 `sku` 參數 (若在 Azure Resource Manager 範本中則使用 `pricingTier`)，[透過 Azure Resource Manager 來設定定價層](../samples/resource-manager-workspace.md)。 

## <a name="legacy-pricing-tiers"></a>舊版定價層

在 2018 年 4 月 2 日之前擁有 Log Analytics 工作區或 Application Insights 資源的訂用帳戶，或所連結的 Enterprise 合約已在 2019 年 2 月 1 日之前啟動的訂用帳戶，將會繼續擁有使用舊版定價層的存取權：**免費**、**獨立 (每 GB)** 和 **每一節點 (OMS)** 。  免費定價層中的工作區會將每日資料擷取量限制在 500 MB 內 ([Azure 資訊安全中心](../../security-center/index.yml)所收集的安全性資料類型除外)，而且資料會限制為保留 7 天。 免費定價層僅供評估之用。 「獨立」或「每一節點」定價層中的工作區則會擁有 30 到 730 天的保留時間，此值可由使用者加以設定。

獨立定價層上的使用量會依內嵌資料量計費。 它會在 **Log Analytics** 服務中報告，且計量會命名為「資料分析」。 

「每一節點」定價層會以小時為單位來針對每一已監視 VM (節點) 收費。 針對每個已監視的節點，工作區每天可獲得 500 MB 資料的配置量，這部分的使用量不會計費。 此配置是以每小時的資料細微性計算，並在每天的工作區層級進行匯總。 高於每日資料配置彙總量的擷取資料則會在資料超額時按 GB 計費。 請注意，如果工作區位於「每一節點」定價層，則 Log Analytics 使用量的服務在帳單上會是 **深入解析與分析**。 使用量會以三個計量回報：

1. 節點：這是受監視節點數目的使用量， (Vm) 單位為節點 * 月數。
2. 每個節點的資料超額：這是內嵌超過匯總資料配置的資料 GB 數目。
3. 每個節點包含的資料：這是匯總資料配置所涵蓋的內嵌資料量。 當工作區在所有定價層中，以顯示 Azure 資訊安全中心所涵蓋的資料量時，也會使用此計量表。

> [!TIP]
> 如果您的工作區可存取 **每一節點** 定價層，但您想要知道其成本是否會低於隨用隨付層，則可以 [使用下面的查詢](#evaluating-the-legacy-per-node-pricing-tier)輕鬆取得建議。 

在 2016 年 4 月前建立的工作區也可以存取原始的 **標準** 和 **進階** 定價層，兩者分別會固定保留資料 30 天和 365 天。 新的工作區則無法建立在 **標準** 或 **進階** 定價層，而且如果將工作區移出這兩層，就無法再將其移回。 這些舊版層的資料內嵌計量稱為「資料分析」。

舊版 Log Analytics 層的使用以及 [Azure 資訊安全中心](../../security-center/index.yml)的使用量計費方式之間還有一些行為。 

1. 如果工作區位於舊版的標準或進階層，Azure 資訊安全中心只會針對 Log Analytics 資料擷取來計費，而不會針對每一節點計費。
2. 如果工作區位於舊版的每一節點層，Azure 資訊安全中心將會使用目前的 [Azure 資訊安全中心節點型定價模型](https://azure.microsoft.com/pricing/details/security-center/)來計費。 
3. 在其他定價層 (包括容量保留) 中，如果 Azure 資訊安全中心是在 2017 年 6 月 19 日前啟用的，則 Azure 資訊安全中心只會針對 Log Analytics 的資料擷取量來計費。 否則，Azure 資訊安全中心會使用目前的 Azure 資訊安全中心節點型定價模型來計費。

您可以在 [Azure 訂用帳戶和服務限制、配額和條件約束](../../azure-resource-manager/management/azure-subscription-service-limits.md#log-analytics-workspaces)中取得更多定價層限制的詳細資料。

所有舊版定價層都沒有以區域為基礎的定價。  

> [!NOTE]
> 若要使用來自購買 OMS E1套件、OMS E2 套件或 OMS Add-On for System Center 的權利，請選擇 Log Analytics [每個節點] 定價層。

## <a name="log-analytics-and-security-center"></a>Log Analytics 和安全性中心

[Azure 資訊安全中心](../../security-center/index.yml) 計費與 Log Analytics 計費密切相關。 資訊安全中心會針對一組 [安全性資料類型](/azure/azure-monitor/reference/tables/tables-category#security) （ (Windowsevent 進行篩選、SecurityAlert、SecurityBaseline、SecurityBaselineSummary、SecurityDetection、SecurityEvent、Windows 防火牆、MaliciousIPCommunication、LinuxAuditLog、SysmonEvent、ProtectionStatus) 和 Update 和 UpdateSummary 資料類型），提供 500 MB/節點/天的配置，但不會在工作區上執行更新管理解決方案或啟用目標解決方案。 如果工作區處於舊版的每個節點定價層，則會結合安全性中心和 Log Analytics 配置，並將其套用至所有可計費的內嵌資料。  

## <a name="change-the-data-retention-period"></a>變更資料保留期

下列步驟說明如何設定您工作區中的記錄資料保留時間。 除非所有工作區都使用舊版的免費定價層，否則可為其設定 30 天到 730 天 (2年) 的資料保留期。[深入了解](https://azure.microsoft.com/pricing/details/monitor/)較長資料保留期的定價。 

### <a name="default-retention"></a>預設保留期

若要設定工作區的預設保留期， 
 
1. 在 Azure 入口網站中，從工作區的左側窗格中選取 [使用量和估計成本]。
2. 在 [使用量和估計成本] 頁面上，按一下頁面頂端的 [資料保留]。
3. 在窗格上，移動滑桿來增加或減少天數，然後按一下 [確定]。  如果您位於「免費」層，將無法修改資料保留期，必須升級至付費層，才能控制此設定。

    ![變更工作區資料保留期設定](media/manage-cost-storage/manage-cost-change-retention-01.png)

當保留期減少時，會在超過新保留設定的資料移除之前的幾天寬限期。 

您也可以使用 `retentionInDays` 參數，[透過 Azure Resource Manager 設定](../samples/resource-manager-workspace.md)保留期。 當您將資料保留期設定為30天，您可以使用參數來觸發立即清除較舊資料的 (，以 `immediatePurgeDataOn30Days` 消除數天的寬限期) 。 這可能適用于合規性相關的案例，也就是必須立即移除資料的情況。 這項立即清除功能只會透過 Azure Resource Manager 公開。 

保留30天的工作區實際上可能保留資料31天。 如果資料一定要保留30天，請使用 Azure Resource Manager 將保留設定為30天，並使用 `immediatePurgeDataOn30Days` 參數。  

根據預設，兩個資料類型（ `Usage` 和 `AzureActivity` -）會保留至少90天，而且此90天保留期不會收費。 如果工作區保留時間增加到超過 90 天，則這些資料類型的保留期也會增加。  這些資料類型也不會有資料擷取費用。 

根據預設，工作區型 Application Insights 資源 (`AppAvailabilityResults`、`AppBrowserTimings`、`AppDependencies`、`AppExceptions`、`AppEvents`、`AppMetrics`、`AppPageViews`、`AppPerformanceCounters`、`AppRequests`、`AppSystemEvents` 和 `AppTraces`) 的資料類型也會保留 90 天，且這 90 天保留期內不會有任何費用。 您可以使用「依資料類型的保留期」功能來調整其保留期。 

請注意，Log Analytics 的[清除 API](/rest/api/loganalytics/workspacepurge/purge) 不會影響保留計費，而且僅適用於非常有限的案例。 若要減少保留費用，您必須針對工作區或特定資料類型來縮減保留期限。 

### <a name="retention-by-data-type"></a>依資料類型的保留期

您也可以為個別資料類型指定 30 天到 730 天不等的保留期設定 (舊版免費定價層中的工作區除外)。 每種資料類型都是工作區的子資源。 例如，SecurityEvent 資料表在 [Azure Resource Manager](../../azure-resource-manager/management/overview.md) 中可以稱為：

```
/subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent
```

請注意，資料類型 (資料表) 會區分大小寫。  若要取得特定資料類型的目前每個資料類型保留設定 (在此範例中為 SecurityEvent) ，請使用：

```JSON
    GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview
```

> [!NOTE]
> 如果已為資料類型明確設定保留，則只會傳回該資料類型的保留。  尚未明確設定保留 (的資料類型，因此會繼承工作區保留) 不會從此呼叫傳回任何資料。 

若要取得工作區中所有資料類型保留集的目前每個資料類型保留設定，請省略特定的資料類型，例如：

```JSON
    GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables?api-version=2017-04-26-preview
```

若要將特定資料類型 (在此範例中為 SecurityEvent) 的保留期設定為 730 天，請執行

```JSON
    PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview
    {
        "properties": 
        {
            "retentionInDays": 730
        }
    }
```

`retentionInDays` 的有效值為 30 到 730。

您無法為 `Usage` 和 `AzureActivity` 資料類型設定自訂保留期。 其會採用預設工作區保留期的最大值或 90 天。 

若要直接連線到 Azure Resource Manager 以設定依資料類型的保留期，最好的工具是 OSS 工具 [ARMclient](https://github.com/projectkudu/ARMClient)。  請透過 [David Ebbo](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) 和 [Daniel Bowbyes](https://blog.bowbyes.co.nz/2016/11/02/using-armclient-to-directly-access-azure-arm-rest-apis-and-list-arm-policy-details/) 的文章來深入了解 ARMclient。  以下是使用 ARMClient 將 SecurityEvent 資料設定為 730 天保留期的範例：

```
armclient PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview "{properties: {retentionInDays: 730}}"
```

> [!TIP]
> 您可以對個別資料類型設定保留期來降低資料保留成本。  針對從 2019 年 10 月 (也就是此功能發行時) 開始收集的資料，減少某些資料類型的保留期可以在一段時間後降低您的保留成本。  針對較早之前收集的資料，為個別類型設定較低的保留期則不會影響您的保留成本。  

## <a name="manage-your-maximum-daily-data-volume"></a>管理您的每日最大資料量

您可以為工作區設定每日上限和限制每日擷取，但請謹慎使用，因為您的目標是不要達到每日限制。  否則，您會遺失當天其餘時間的資料，這可能會影響其功能取決於工作區中一直在提供的最新資料的其他 Azure 服務和解決方案。  如此一來，您在資源健全狀況支援 IT 服務時觀察和接收警示的功能會受到影響。  每日上限旨在用來從您的受控資源管理非預期的資料量 **增加** ，並保持在您的限制內，或當您想要限制工作區未計畫的費用時。 不適合設定每日上限，以便符合每日都能符合工作區的限制。

每個工作區的每日上限都套用於一天的不同小時。 重設時數會顯示在 [ **每日上限** ] 頁面中 (查看以下) 。 無法設定此重設時數。 

達到每日限制後不久，就會立即停止收集當天剩餘時間的需計費資料類型。 套用每日上限的延遲是指在指定的每日上限層級中，不會精確套用端點。 在所選取的 Log Analytics 工作區中，頁面頂端會出現警告橫幅，且系統會將作業事件傳送至 **LogManagement** 類別下的「Operation」 資料表。 一旦過了「每日限制的設定時間」下定義的重設時間後，資料收集就會繼續執行。 建議您根據此作業事件定義警示規則，設定為在達到每日資料限制時通知 (請參閱 [以下](#alert-when-daily-cap-reached)) 。 

> [!NOTE]
> 每日上限無法精確地以指定的端點層級和某些多餘的資料來停止收集資料，尤其是當工作區接收大量資料時。 請參閱 [下](#view-the-effect-of-the-daily-cap) 圖，以瞭解有助於研究每日上限行為的查詢。 

> [!WARNING]
> 每日上限不會停止收集 Azure 當做或 Azure 資訊安全中心的資料，但在2017年6月19日前安裝 Azure 資訊安全中心的工作區除外。 

### <a name="identify-what-daily-data-limit-to-define"></a>識別要定義的每日資料限制

檢閱 [Log Analytics 使用量和估計成本](usage-estimated-costs.md)，以了解資料擷取趨勢及要定義的每日資料量上限。 此謹慎考量，因為達到限制之後，您將無法監視您的資源。 

### <a name="set-the-daily-cap"></a>設定每日上限

下列步驟說明如何設定限制，以管理 Log Analytics 工作區每日擷取的資料量。  

1. 在工作區中，從左側窗格中選取 [使用量和估計成本]。
2. 在所選工作區的 [ **使用量和估計成本** ] 頁面上，按一下頁面頂端的 [ **資料上限** ]。 
3. 每日上限預設為 [關閉] 嗎？ 請按一下 [開啟] 來加以啟用，然後設定資料量限制 (GB/天)。

    ![Log Analytics 設定資料限制](media/manage-cost-storage/set-daily-volume-cap-01.png)
    
您可以設定底下的 `dailyQuotaGb` 參數（ `WorkspaceCapping` 如 [工作區-建立或更新](/rest/api/loganalytics/workspaces/createorupdate#workspacecapping)所述），以透過 ARM 設定每日上限。 

### <a name="view-the-effect-of-the-daily-cap"></a>查看每日上限的效果

若要查看每日上限的效果，請務必考慮不包含在每日上限中的安全性資料類型，以及工作區的重設時數。 每日上限重設時數會顯示在 **每日的上限** 頁面中。  下列查詢可用來追蹤每日上限重設之間，受限於每日上限的資料量。 在此範例中，工作區的重設時數為14:00。  您必須為您的工作區更新此程式。

```kusto
let DailyCapResetHour=14;
Usage
| where Type !in ("SecurityAlert", "SecurityBaseline", "SecurityBaselineSummary", "SecurityDetection", "SecurityEvent", "WindowsFirewall", "MaliciousIPCommunication", "LinuxAuditLog", "SysmonEvent", "ProtectionStatus", "WindowsEvent")
| extend TimeGenerated=datetime_add("hour",-1*DailyCapResetHour,TimeGenerated)
| where TimeGenerated > startofday(ago(31d))
| where IsBillable
| summarize IngestedGbBetweenDailyCapResets=sum(_BilledSize)/1000. by day=bin(TimeGenerated, 1d) | render areachart  
```

### <a name="alert-when-daily-cap-reached"></a>已達每日上限時發出警示

雖然我們會在您達到資料限制閾值時，於 Azure 入口網站中顯示視覺提示，但對於需要立刻處理的作業問題，此行為並不一定與您的管理方式一致。  若要接收警示通知，您可以在 Azure 監視器中建立新的警示規則。  若要進一步了解，請參閱[如何建立、檢視及管理警示](alerts-metric.md)。

為協助您開始使用，以下是使用函式來查詢資料表之警示的建議設定 `Operation` `_LogOperation` 。 

- 目標：選取您的 Log Analytics 資源
- 準則： 
   - 訊號名稱：自訂記錄搜尋
   - 搜尋查詢： `_LogOperation | where Category == "Ingestion" | where Operation == "Ingestion rate" | where Level == "Warning"`
   - 依據：結果數目
   - 條件：大於
   - 閾值：0
   - 期間：5 (分鐘)
   - 頻率：5 (分鐘)
- 警示規則名稱：已達到每日資料限制
- 嚴重性：警告 (嚴重性 1)

一旦定義警示且達到限制後，警示就會觸發，並執行動作群組中定義的回應。 其可以透過電子郵件和文字簡訊通知您的小組，或使用 Webhook、自動化 Runbook 或[與外部 ITSM 方案整合](itsmc-overview.md#create-itsm-work-items-from-azure-alerts)來自動採取動作。 

## <a name="troubleshooting-why-usage-is-higher-than-expected"></a>針對使用量高於預期的原因進行疑難排解

較高的使用量是由下列一個或兩個原因所造成：
- 比預期更多的節點將資料傳送到 Log Analytics 工作區
- 傳送到 Log Analytics 工作區的資料比預期更多 (可能是因為開始使用新的解決方案或變更了現有解決方案的設定)

## <a name="understanding-nodes-sending-data"></a>了解傳送資料的節點

若要了解上個月每一天從代理程式報告活動訊號的節點數目，請使用

```kusto
Heartbeat 
| where TimeGenerated > startofday(ago(31d))
| summarize nodes = dcount(Computer) by bin(TimeGenerated, 1d)    
| render timechart
```
若要取得過去 24 小時內傳送資料的節點計數，請使用查詢： 

```kusto
find where TimeGenerated > ago(24h) project Computer
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize nodes = dcount(computerName)
```

若要取得傳送任何資料的節點清單 (以及每個節點傳送的資料量)，則可以使用下列查詢：

```kusto
find where TimeGenerated > ago(24h) project _BilledSize, Computer
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize TotalVolumeBytes=sum(_BilledSize) by computerName
```

### <a name="nodes-billed-by-the-legacy-per-node-pricing-tier"></a>依每個節點的舊版計費的節點定價層

[舊版的每個節點定價層](#legacy-pricing-tiers)會針對具有每小時資料細微性的節點計費，而且也不會計算只傳送一組安全性資料類型的節點。 每日節點的計數會接近下列查詢：

```kusto
find where TimeGenerated >= startofday(ago(7d)) and TimeGenerated < startofday(now()) project Computer, _IsBillable, Type, TimeGenerated
| where Type !in ("SecurityAlert", "SecurityBaseline", "SecurityBaselineSummary", "SecurityDetection", "SecurityEvent", "WindowsFirewall", "MaliciousIPCommunication", "LinuxAuditLog", "SysmonEvent", "ProtectionStatus", "WindowsEvent")
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| where _IsBillable == true
| summarize billableNodesPerHour=dcount(computerName) by bin(TimeGenerated, 1h)
| summarize billableNodesPerDay = sum(billableNodesPerHour)/24., billableNodeMonthsPerDay = sum(billableNodesPerHour)/24./31.  by day=bin(TimeGenerated, 1d)
| sort by day asc
```

帳單上的單位數目是以節點 * 月數為單位，以 `billableNodeMonthsPerDay` 查詢表示。 如果工作區已安裝更新管理方案，請將 Update 和 UpdateSummary 資料類型加入至上述查詢中 where 子句的清單。 最後，當使用未在上述查詢中表示的方案目標時，實際的計費演算法會有一些額外的複雜性。 


> [!TIP]
> 請謹慎使用這些 `find` 查詢，因為執行跨資料類型掃描會[耗用大量資源](../log-query/query-optimization.md#query-performance-pane)。 如果您不需要 **每一部電腦** 的結果，則請查詢 Usage 資料類型 (請參閱下文)。

## <a name="understanding-ingested-data-volume"></a>了解已擷取的資料量

在 [使用量和估計成本] 頁面上，[每個解決方案的資料擷取] 圖表會顯示所傳送的資料總量，以及每個解決方案所傳送的資料量。 這可讓您判斷各種趨勢，例如整體資料使用量 (或特定解決方案的使用量) 是否正在增長、穩定不變或正在減少。 

### <a name="data-volume-for-specific-events"></a>特定事件的資料量

若要查看特定一組事件的已擷取資料大小，您可以查詢特定資料表 (在此範例中為 `Event`)，然後將查詢限制在感興趣的事件 (在此範例中為事件識別碼 5145 或 5156)：

```kusto
Event
| where TimeGenerated > startofday(ago(31d)) and TimeGenerated < startofday(now()) 
| where EventID == 5145 or EventID == 5156
| where _IsBillable == true
| summarize count(), Bytes=sum(_BilledSize) by EventID, bin(TimeGenerated, 1d)
``` 

請注意，子句 `where _IsBillable = true` 會篩選掉來自無擷取成本之特定解決方案的資料類型。 [深入瞭解](./log-standard-columns.md#_isbillable) `_IsBillable` 。

### <a name="data-volume-by-solution"></a>依方案分類的資料量

若要檢視上個月 (不含未滿一天的最後一天) 依解決方案分類的可計費資料量，所用的查詢為：

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by bin(StartTime, 1d), Solution | render barchart
```

具有 `TimeGenerated` 的子句只是為了確保 Azure 入口網站中的查詢體驗會回顧超過預設的 24 小時。 在使用 Usage 資料類型時，`StartTime` 和 `EndTime` 代表所顯示結果所屬的時段。 

### <a name="data-volume-by-type"></a>資料量 (依類型)

您可以進一步向下切入，以查看依資料類型的資料趨勢：

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by bin(StartTime, 1d), DataType | render barchart
```

或者，若要查看上個月的資料表 (依解決方案和類型)，

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) by Solution, DataType
| sort by Solution asc, DataType asc
```

### <a name="data-volume-by-computer"></a>資料量 (依電腦)

`Usage` 資料類型不包含電腦層級的資訊。 若要查看每一部電腦已擷取的資料 **大小**，請使用會提供大小 (以位元組為單位) 的 `_BilledSize` [屬性](./log-standard-columns.md#_billedsize)：

```kusto
find where TimeGenerated > ago(24h) project _BilledSize, _IsBillable, Computer
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize BillableDataBytes = sum(_BilledSize) by  computerName 
| sort by BillableDataBytes nulls last
```

`_IsBillable` [屬性](./log-standard-columns.md#_isbillable)會指定已擷取的資料是否會產生費用。 

若要查看每一部電腦所擷取的可計費事件 **計數**，請使用 

```kusto
find where TimeGenerated > ago(24h) project _IsBillable, Computer
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize eventCount = count() by computerName  
| sort by eventCount nulls last
```

> [!TIP]
> 請謹慎使用這些 `find` 查詢，因為執行跨資料類型掃描會[耗用大量資源](../log-query/query-optimization.md#query-performance-pane)。 如果您不需要 **每一部電腦** 的結果，則請查詢 Usage 資料類型。

### <a name="data-volume-by-azure-resource-resource-group-or-subscription"></a>資料量 (依 Azure 資源、資源群組或訂用帳戶)

針對 Azure 中所裝載節點的資料，您可以取得 __每一部電腦__ 所擷取資料的 **大小**，使用 _ResourceId [屬性](./log-standard-columns.md#_resourceid)，此屬性會提供資源的完整路徑：

```kusto
find where TimeGenerated > ago(24h) project _ResourceId, _BilledSize, _IsBillable
| where _IsBillable == true 
| summarize BillableDataBytes = sum(_BilledSize) by _ResourceId | sort by BillableDataBytes nulls last
```

針對裝載于 Azure 中節點的資料，您可以取得 __每個 azure 訂__ 用帳戶的內嵌資料 **大小**，請使用 `_SubscriptionId` 下列屬性：

```kusto
find where TimeGenerated > ago(24h) project _ResourceId, _BilledSize, _IsBillable
| where _IsBillable == true 
| summarize BillableDataBytes = sum(_BilledSize) by _ResourceId
| summarize BillableDataBytes = sum(BillableDataBytes) by _SubscriptionId | sort by BillableDataBytes nulls last
```

若要依資源群組取得資料量，您可以剖析 `_ResourceId` ：

```kusto
find where TimeGenerated > ago(24h) project _ResourceId, _BilledSize, _IsBillable
| where _IsBillable == true 
| summarize BillableDataBytes = sum(_BilledSize) by _ResourceId
| extend resourceGroup = tostring(split(_ResourceId, "/")[4] )
| summarize BillableDataBytes = sum(BillableDataBytes) by resourceGroup | sort by BillableDataBytes nulls last
```

若有需要，您也可以使用來剖析 `_ResourceId` 更完整的

```Kusto
| parse tolower(_ResourceId) with "/subscriptions/" subscriptionId "/resourcegroups/" 
    resourceGroup "/providers/" provider "/" resourceType "/" resourceName   
```

> [!TIP]
> 請謹慎使用這些 `find` 查詢，因為執行跨資料類型掃描會[耗用大量資源](../log-query/query-optimization.md#query-performance-pane)。 如果您不需要每一訂用帳戶、資源群組或資源名稱的結果，請查詢 Usage 資料類型。

> [!WARNING]
> [使用量] 資料類型的部分欄位雖然仍位於結構描述中，但皆已過時，且系統將不再填入其值。 這包括 [Computer]，以及其他與擷取相關的欄位 ([TotalBatches]、[BatchesWithinSla]、[BatchesOutsideSla]、[BatchesCapped]，以及 [AverageProcessingTimeMs])。


### <a name="querying-for-common-data-types"></a>查詢常見的資料類型

若要更深入挖掘特定資料類型的資料來源，以下是一些實用的範例查詢：

+ **工作區型 Application Insights** 資源
  - 深入瞭解如何 [管理 Application Insights 的使用量和成本](../app/pricing.md#data-volume-for-workspace-based-application-insights-resources)
+ **安全性** 解決方案
  - `SecurityEvent | summarize AggregatedValue = count() by EventID`
+ **記錄管理** 解決方案
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

## <a name="tips-for-reducing-data-volume"></a>降低成本的訣竅

降低所收集記錄量的一些建議包括：

| 高資料量的來源 | 如何縮減資料量 |
| -------------------------- | ------------------------- |
| 容器深入解析         | [設定容器深入](../insights/container-insights-cost.md#controlling-ingestion-to-reduce-cost) 解析，只收集您所需的資料。 |
| 安全性事件            | 選取[一般或最小安全性事件](../../security-center/security-center-enable-data-collection.md#data-collection-tier) <br> 變更安全性稽核原則為只收集所需事件。 特別檢閱下列原則是否需要收集事件： <br> - [a稽核篩選平台](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772749(v=ws.10)) <br> - [稽核登錄](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941614(v%3dws.10))<br> - [稽核檔案系統](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772661(v%3dws.10))<br> - [稽核核心物件](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941615(v%3dws.10))<br> - [稽核控制代碼操作](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772626(v%3dws.10))<br> - 稽核抽取式存放裝置 |
| 效能計數器       | 變更[效能計數器組態](data-sources-performance-counters.md)以： <br> - 減少收集頻率 <br> - 減少效能計數器的數目 |
| 事件記錄                 | 變更[事件記錄組態](data-sources-windows-events.md)以： <br> - 減少所收集的事件記錄數目 <br> - 只收集必要的事件層級。 例如，不要收集「資訊」層級事件 |
| syslog                     | 變更 [Syslog 組態](data-sources-syslog.md)以： <br> - 減少所收集的設施數目 <br> - 只收集必要的事件層級。 例如，不要收集「資訊」和「偵錯」層級事件 |
| AzureDiagnostics           | 將 [資源記錄檔收集](./diagnostic-settings.md#create-in-azure-portal) 變更為： <br> - 減少會將記錄傳送至 Log Analytics 的資源數目 <br> - 只收集必要的記錄 |
| 電腦中不需要解決方案的方案資料 | 使用[方案目標](../insights/solution-targeting.md)，只從必要的電腦群組收集資料。 |

### <a name="getting-nodes-as-billed-in-the-per-node-pricing-tier"></a>在每一節點定價層讓節點成為已計費

若要在工作區位於舊版的每一節點定價層時，取得會以節點形式計費的電腦清單，請尋找會傳送 **已計費資料類型** (某些資料類型是免費的) 的節點。 若要這麼做，請使用 `_IsBillable` [屬性](./log-standard-columns.md#_isbillable)，並使用完整網域名稱的最左邊欄位。 這會傳回具有每小時 (這是節點的計算和計費細微性) 已計費資料的電腦計數：

```kusto
find where TimeGenerated > ago(24h) project Computer, TimeGenerated
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize billableNodes=dcount(computerName) by bin(TimeGenerated, 1h) | sort by TimeGenerated asc
```

### <a name="getting-security-and-automation-node-counts"></a>取得安全性和自動化節點計數

如果您是處於「每節點 (OMS)」定價層，則系統會根據您使用的節點和解決方案數目來向您收取費用，您需支付費用的見解與分析節點數目將會顯示在 [使用量和估計成本] 頁面上的資料表中。  

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

## <a name="evaluating-the-legacy-per-node-pricing-tier"></a>評估舊版的每一節點定價層

在決定可存取舊版 **每一節點** 定價層的工作區是在該階層還是目前的 **隨用隨付** 或 **容量保留** 層會比較好時，客戶往往很難做出評估。  這牽涉到了解每一節點定價層中每一已監視節點的固定成本與其內含的資料配置「500 MB/節點/天」，以及隨用隨付 (每 GB) 層只需支付所擷取資料的成本之間的取捨。 

為了協助進行此評估，您可以根據工作區的使用模式，使用下列查詢來提出最佳定價層建議。  此查詢會查看過去 7 天內已監視的節點和擷取至工作區的資料，並針對每一天評估哪個定價層最好。 若要使用查詢，您必須指定

1. 工作區是否使用 Azure 資訊安全中心，方法是將 `workspaceHasSecurityCenter` 設定為 `true` 或 `false`， 
2. 如果您有特定折扣，請更新價格，並
3. 藉由設定 `daysToEvaluate`，指定要回顧和分析的天數。 如果查詢花太長的時間嘗試查看 7 天的資料，這麼做會很有用。 

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
union * 
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

此查詢雖然不會精確複製使用量的計算方式，但可在大部分情況下提供定價層建議。  

> [!NOTE]
> 若要使用來自購買 OMS E1套件、OMS E2 套件或 OMS Add-On for System Center 的權利，請選擇 Log Analytics [每個節點] 定價層。

## <a name="create-an-alert-when-data-collection-is-high"></a>在資料收集量很高時建立警示

本節說明如何使用 Azure 監視器 [記錄警示](alerts-unified-log.md)，在過去24小時內建立資料量超過指定數量的警示。 

若要在過去24小時內內嵌計費資料量超過 50 GB 時發出警示，請遵循下列步驟： 

- **定義警示條件**：將您的 Log Analytics 工作區指定為資源目標。
- **警示準則**：指定下列項目：
   - **訊號名稱**：選取 [自訂記錄搜尋]
   - **搜尋查詢** 至 `Usage | where IsBillable | summarize DataGB = sum(Quantity / 1000.) | where DataGB > 50` 。 如果您想要不同的 
   - [警示邏輯] 為 [根據結果數目]，而 [條件] 為 [大於臨界值 0]
   - 每隔 *1440* 分鐘一天執行一次的時間（ *1440* 分鐘）和 **警示頻率** 的 **時間週期**。
- **定義警示詳細資料**：指定下列項目：
   - *在24小時內大於 50 GB 的可計費資料磁片* 區 **名稱**
   - 將 [嚴重性] 設定為「警告」

指定現有或建立新的[動作群組](action-groups.md)，以便在記錄警示符合準則時收到通知。

當您收到警示時，請使用上述各節中的步驟，以瞭解使用方式高於預期的原因。

## <a name="data-transfer-charges-using-log-analytics"></a>使用 Log Analytics 的資料傳輸費用

將資料傳送至 Log Analytics 可能會產生資料頻寬費用，但它僅限於已安裝 Log Analytics 代理程式的虛擬機器，並在使用診斷設定或與內建于 Azure Sentinel 的其他連接器時不適用。 如 [Azure 頻寬定價頁面](https://azure.microsoft.com/pricing/details/bandwidth/)所述，在位於兩個區域的 Azure 服務之間資料傳輸時，會依正常費率向輸出資料傳輸收費。 輸入資料傳輸則是免費的。 不過，相較於 Log Analytics 的資料擷取成本，這項費用很低 (幾 %)。 因此，您必須專注于 [內嵌資料磁片](#understanding-ingested-data-volume)區，才能控制 Log Analytics 的成本。 


## <a name="troubleshooting-why-log-analytics-is-no-longer-collecting-data"></a>針對 Log Analytics 為什麼不再收集資料的問題進行疑難排解

如果您處於舊版「免費」定價層並在某日傳送了超過 500 MB 的資料，就會停止收集當日的其餘資料。 達到每日限制是 Log Analytics 停止收集資料或資料似乎遺失的常見原因。  當資料收集開始及停止時，Log Analytics 會建立 Operation 類型的事件。 在搜尋中執行下列查詢，即可檢查您是否達到每日限制並遺失資料： 

```kusto
Operation | where OperationCategory == 'Data Collection Status'
```

當資料收集停止時，OperationStatus 為 **Warning**。 當資料收集開始時，OperationStatus 為 **Succeeded**。 下表描述資料收集停止的原因，並建議為繼續資料收集所要採取的動作：  

|收集停止的原因| 解決方法| 
|-----------------------|---------|
|已達您工作區的每日上限|等到自動重新開始收集或提高每日資料量限制，如「管理每日資料量上限」中所述。 每日上限重設時間會顯示在 **每日的上限** 頁面上。 |
| 您的工作區已達到 [資料內嵌數量的速率](../service-limits.md#log-analytics-workspaces) | 使用診斷設定從 Azure 資源傳送資料的預設內嵌磁碟區速率限制，約為每個工作區 6 GB/分鐘。 這是估計值，因為根據記錄長度和其壓縮比率，實際大小可能會因資料類型有所不同。 此限制不適用於從代理程式或資料收集器 API 傳送的資料。 如果您以較高的速率將資料傳送至單一工作區，則系統會卸除某些資料，且每隔 6 小時會將事件傳送至工作區中的作業資料表，同時會繼續超過閾值。 如果您的內嵌磁碟區持續超過速率限制，或您希望很快能達到速率限制，可以傳送電子郵件至 LAIngestionRate@microsoft.com 或開啟支援要求，要求增加工作區。 要尋找的事件指出，查詢 `Operation | where OperationCategory == "Ingestion" | where Detail startswith "The rate of data crossed the threshold"`可找到資料的內嵌速率限制。 |
|已達舊版免費定價層的每日限制 |請等到隔天自動重新開始收集，或變更為付費定價層。|
|Azure 訂用帳戶處於暫停狀態，原因如下：<br> 免費試用已結束<br> Azure Pass 已過期<br> 已達每月消費限制 (例如 MSDN 或 Visual Studio 訂閱)|轉換成付費訂閱<br> 移除限制，或等到限制重設|

若要在資料收集停止時收到通知，請使用 *建立每日資料上限* 警示中所述的步驟，以在資料收集停止時收到通知。 使用[建立動作群組](action-groups.md)中所述的步驟來設定警示規則的電子郵件、Webhook 或 Runbook 動作。 

## <a name="limits-summary"></a>限制摘要

Log Analytics 還有一些限制，其中的某些限制取決於 Log Analytics 定價層。 這些檔記載于 [Azure 訂用帳戶和服務限制、配額和條件約束](../../azure-resource-manager/management/azure-subscription-service-limits.md#log-analytics-workspaces)。


## <a name="next-steps"></a>後續步驟

- 請參閱 [Azure 監視器記錄中的記錄搜尋](../log-query/log-query-overview.md)，以了解如何使用搜尋語言。 您可以使用搜尋查詢，對使用量資料執行額外的分析。
- 使用[建立新的記錄警示](alerts-metric.md)中所述的步驟，可在符合搜尋條件時收到通知。
- 使用[方案目標](../insights/solution-targeting.md)，只從必要的電腦群組收集資料。
- 若要設定有效的事件收集原則，請檢閱 [Azure 資訊安全中心篩選原則](../../security-center/security-center-enable-data-collection.md)。
- 變更[效能計數器組態](data-sources-performance-counters.md)。
- 若要修改事件收集設定，請檢閱[事件記錄組態](data-sources-windows-events.md)。
- 若要修改 syslog 收集設定，請檢閱 [Syslog 組態](data-sources-syslog.md)。
