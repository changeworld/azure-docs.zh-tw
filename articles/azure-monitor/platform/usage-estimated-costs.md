---
title: 在 Azure 監視器中監視使用量和估計成本
description: 使用 Azure 監視器的使用量和估計成本頁面的程序概觀
author: dalekoetke
services: azure-monitor
ms.topic: conceptual
ms.date: 10/28/2019
ms.author: mbullwin
ms.reviewer: Dale.Koetke
ms.subservice: ''
ms.openlocfilehash: 22c7243cd966f458610c2abc67ed5540b37357b8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "77658810"
---
# <a name="monitoring-usage-and-estimated-costs-in-azure-monitor"></a>在 Azure 監視器中監視使用量和估計成本

> [!NOTE]
> 本文說明如何跨多項 Azure 監視功能來查看使用量和估計成本。 Azure 監視器特定元件的相關文章包括：
> - [使用 Azure 監視器記錄來管理使用量和成本](manage-cost-storage.md)說明如何藉由變更資料保留期限來控制成本，以及如何分析和警示您的資料使用量。
> - [管理 Application Insights 的使用量和成本](../../azure-monitor/app/pricing.md)說明如何分析 Application Insights 中的資料使用量。

## <a name="azure-monitor-pricing-model"></a>Azure 監視器計價模式

基本 Azure 監視器計費模型是以雲端為基礎、以耗用量為基礎的定價（「隨用隨付」）。 您只需依據使用量付費。 定價詳細資料適用于[警示、計量、通知](https://azure.microsoft.com/pricing/details/monitor/)、 [Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/)和[Application Insights](https://azure.microsoft.com/pricing/details/application-insights/)。 

除了記錄資料的隨用隨付模式以外，Log Analytics 也具有容量保留，可讓您省下25% 的費用（相較于隨用隨付價格）。 容量保留定價可讓您從「100 GB/天」起購買保留。 高於保留層級的使用量則會以隨用隨付費率計費。 [深入瞭解](https://azure.microsoft.com/pricing/details/monitor/)容量保留定價。

有些客戶可以存取舊版的[Log Analytics 定價](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#legacy-pricing-tiers)層和[舊版企業 Application Insights 定價層](https://docs.microsoft.com/azure/azure-monitor/app/pricing#legacy-enterprise-per-node-pricing-tier)。 

## <a name="understanding-your-azure-monitor-costs"></a>瞭解您的 Azure 監視器成本

有兩個階段可瞭解成本。 第一種是考慮 Azure 監視器作為監視解決方案。 

### <a name="estimating-the-costs-to-manage-your-environment"></a>估算環境管理成本

如果您尚未使用 Azure 監視器記錄，您可以使用[Azure 監視器定價計算機](https://azure.microsoft.com/pricing/calculator/?service=monitor)來估計使用 Azure 監視器的成本。 一開始請先在 [搜尋] 方塊中輸入「Azure 監視器」，然後按一下產生的 [Azure 監視器] 圖格。 向下展開頁面以 Azure 監視器，然後從 [類型] 下拉式清單中選取其中一個選項：

- 計量查詢和警示  
- Log Analytics
- Application Insights

在上述每一種情況下，定價計算機可協助您根據預期的使用量來預估可能的成本。

例如，您可以使用 Log Analytics 來輸入 Vm 的數目，以及您預期要從每個 VM 收集的 GB 資料。 通常是從一般 Azure VM 內嵌 1 GB 到 3 GB 的資料月。 如果您已經在評估 Azure 監視器記錄，則可以使用自己環境中的資料統計值。 請參閱下文以了解如何判斷[已監視的 VM 數目](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-nodes-sending-data)以及[您的工作區所擷取的資料量](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-ingested-data-volume)。

同樣地，針對 Application Insights，如果您啟用「根據應用程式活動估計資料量」功能，您可以提供應用程式的輸入（每月個要求和每月的頁面流覽次數），然後計算機會告訴您類似應用程式所收集的資料中間值和第90個百分位數。 這些應用程式跨越 Application Insights 設定的範圍 (例如，有些有預設的取樣，有些沒有取樣等等)，因此，您仍然可以使用取樣，控制將擷取的資料量減少到遠低於中位數層級以下。 但這是了解其他類似客戶看法的起點。 [深入瞭解](https://docs.microsoft.com/azure/azure-monitor/app/pricing#estimating-the-costs-to-manage-your-application)Application Insights 的預估成本。

### <a name="understanding-your-usage-and-estimated-costs"></a>瞭解您的使用量和估計成本

請務必瞭解並使用 Azure 監視器來追蹤您的使用方式，而且有豐富的工具組可協助此功能。 

Azure 在 [Azure 成本管理 + 計費](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis?toc=/azure/billing/TOC.json)中樞內提供了許多實用的功能。 開啟**Azure 成本管理 + 計費**中樞之後，請按一下 [**成本管理**]，然後選取 [[範圍](https://docs.microsoft.com/azure/cost-management/understand-work-scopes)] （要調查的資源集合）。 

然後，若要查看過去30天的 Azure 監視器成本，請按一下 [**每日成本**] 磚，選擇 [相對日期] 底下的 [過去30天]，然後新增篩選來選取服務名稱：

1. Azure 監視器
2. Application Insights
3. Log Analytics
4. 深入解析與分析

這會產生如下所示的視圖：

![Azure 成本管理螢幕擷取畫面](./media/usage-estimated-costs/010.png)

從這裡開始，您可以從這個累積的成本摘要切入，以取得「成本資源」視圖中的更詳細資料。 在目前的定價層中，Azure 記錄資料是以相同的計量集計費，不論其源自于 Log Analytics 或 Application Insights。 若要將成本與 Log Analytics 或 Application Insights 使用量分開，您可以在**資源類型**上新增篩選。 若要查看所有 Application Insights 成本，請將資源類型篩選為 [microsoft Insights/元件]，並針對 Log Analytics 成本，將 [資源類型] 篩選為 [microsoft.operationalinsights/工作區]。 

您可以[從 Azure 入口網站下載使用量](https://docs.microsoft.com/azure/billing/billing-download-azure-invoice-daily-usage-date#download-usage-in-azure-portal)，以取得更多使用方式的詳細資料。 在下載的試算表中，您可以看到每天每個 Azure 資源的使用量。 在此 Excel 試算表中，您可以藉由先篩選「計量類別」資料行來顯示「Application Insights」和「Log Analytics」，然後在「包含 microsoft.insights/components」的「執行個體識別碼」資料行上新增篩選條件，來找到您的 Application Insights 資源使用量。  由於所有 Azure 監視器元件都有單一記錄後端，因此，大部分 Application Insights 使用量都會以 Log Analytics 計量類別的計量報告。  只有舊版定價層和多重步驟 Web 測試的 Application Insights 資源，會以 Application Insights 的計量類別來報告。  使用量會顯示在 [已取用的數量] 資料行，每個項目的單位則會顯示在 [測量單位] 資料行。  有更多詳細資料可協助您[了解 Microsoft Azure 帳單](https://docs.microsoft.com/azure/billing/billing-understand-your-bill)。 

> [!NOTE]
> 在「 **Azure 成本管理 + 計費**中樞」中使用**成本管理**是廣泛瞭解監視成本的慣用方法。  [Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understand-your-usage-and-estimate-costs)和[Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/pricing#understand-your-usage-and-estimate-costs)的**使用量和估計成本**經驗，為 Azure 監視器的每個部分提供更深入的見解。

查看 Azure 監視器使用量的另一個選項是監視中樞內的 [**使用量和估計成本**] 頁面。 這會顯示核心監視功能（例如[警示、計量、通知](https://azure.microsoft.com/pricing/details/monitor/)、 [Azure Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/)和[Azure 應用程式深入](https://azure.microsoft.com/pricing/details/application-insights/)解析）的使用方式。 客戶若是採用在 2018 年 4 月前提供的定價方案，則也會提供透過深入解析與分析供應項目購買的 Log Analytics 使用量。

在此頁面上，使用者可以檢視他們在過去 31 天內對這些資源的使用量 (依個別訂用帳戶彙總)。 `Drill-ins`顯示31天期間內的使用量趨勢。 系統必須彙整許多資料才能產生此預估值，因此請耐心等候頁面載入。

下列範例顯示監視使用量和估計最終成本的情形：

![使用量和估計成本入口網站螢幕擷取畫面](./media/usage-estimated-costs/001.png)

選取 [每月使用量] 資料行中的連結以開啟一個圖表，顯示過去 31 天的使用量趨勢： 

![每個節點包含長條圖的螢幕擷取畫面](./media/usage-estimated-costs/002.png)

## <a name="operations-management-suite-subscription-entitlements"></a>Operations Management Suite 訂用帳戶權利

凡購買 Microsoft Operations Management Suite E1 和 E2 的客戶，皆符合資格使用 [Log Analytics](https://www.microsoft.com/cloud-platform/operations-management-suite) 和 [Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-pricing) 的按節點資料擷取權利。 若要以指定訂用帳戶享用 Log Analytics 工作區或 Application Insights 資源的這些權利： 

- Log Analytics 工作區應該使用「每個節點 (OMS)」定價層。
- Application Insights 資源應該使用「企業」定價層。

視貴組織購買的套件節點數目而定，將一些訂用帳戶移至隨用隨付（每 GB）定價層可能很有利，但這需要謹慎考慮。

> [!WARNING]
> 如果您的組織目前 Microsoft Operations Management Suite E1 和 E2，通常最好將您的 Log Analytics 工作區保留在「每節點（OMS）」定價層，並將您的 Application Insights 資源放在「企業」定價層中。 
>
