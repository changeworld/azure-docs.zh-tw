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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658810"
---
# <a name="monitoring-usage-and-estimated-costs-in-azure-monitor"></a>在 Azure 監視器中監視使用量和估計成本

> [!NOTE]
> 本文介紹如何查看多個 Azure 監視功能的使用方式和估計成本。 Azure 監視器特定元件的相關文章包括：
> - [使用 Azure 監視器日誌管理使用方式和成本](manage-cost-storage.md)介紹了如何通過更改資料保留期來控制成本，以及如何分析和警報資料使用方式。
> - [管理應用程式見解的使用和成本](../../azure-monitor/app/pricing.md)描述如何在應用程式見解中分析資料使用方式。

## <a name="azure-monitor-pricing-model"></a>Azure 監視器定價模型

基本 Azure 監視器計費模型是雲友好型、基於消費的定價（"即用即付"）。 您只需依據使用量付費。 定價詳細資訊可用於[警報、指標、通知](https://azure.microsoft.com/pricing/details/monitor/)、[日誌分析和](https://azure.microsoft.com/pricing/details/log-analytics/)[應用程式見解](https://azure.microsoft.com/pricing/details/application-insights/)。 

除了日誌資料的即用即付模型外，日誌分析還具有容量預留功能，與即用即付價格相比，您可以節省多達 25% 的費用。 容量預訂定價使您能夠從 100 GB/天開始購買預訂。 任何超出預訂級別的使用量都將以即用即付費率計費。 [詳細瞭解](https://azure.microsoft.com/pricing/details/monitor/)容量預訂定價。

某些客戶將有權訪問[舊日誌分析定價層](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#legacy-pricing-tiers)和[舊企業應用程式見解定價層](https://docs.microsoft.com/azure/azure-monitor/app/pricing#legacy-enterprise-per-node-pricing-tier)。 

## <a name="understanding-your-azure-monitor-costs"></a>瞭解 Azure 監視器成本

瞭解成本有兩個階段。 第一種是考慮 Azure 監視器作為監視解決方案。 

### <a name="estimating-the-costs-to-manage-your-environment"></a>估算管理環境的成本

如果尚未使用 Azure 監視器日誌，則可以使用[Azure 監視器定價計算機](https://azure.microsoft.com/pricing/calculator/?service=monitor)來估計使用 Azure 監視器的成本。 首先在"搜索"框中輸入"Azure 監視器"，然後按一下生成的 Azure 監視器磁貼。 向下滾動頁面到 Azure 監視器，然後從"類型"下拉清單中選擇其中一個選項：

- 指標查詢和警報  
- Log Analytics
- Application Insights

在每一個中，定價計算機將説明您根據預期利用率估計您的可能成本。

例如，使用日誌分析，您可以輸入要從每個 VM 收集的 VM 數量和 GB 資料。 通常從典型的 Azure VM 中攝錄 1 GB 到 3 GB 的資料月。 如果您已經正在評估 Azure 監視器日誌，則可以使用您自己的環境中的資料統計資訊。 有關如何確定[受監視 VM 的數量](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-nodes-sending-data)以及[工作區正在攝用的資料量](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-ingested-data-volume)，請參閱下文。

同樣，對於應用程式見解，如果啟用"基於應用程式活動估計資料量"功能，則可以提供有關應用程式的輸入（每月請求和每月頁面流覽，以防收集用戶端遙測），然後計算機將告訴您類似應用程式收集的資料的中位數和 90 百分位。 這些應用程式涵蓋應用程式見解配置的範圍（例如，有些應用程式具有預設採樣，有些沒有採樣等），因此您仍具有使用採樣將所引入的資料量減少到遠低於中位數水準的控制項。 但這是瞭解其他類似客戶所看到的一個起點。 [詳細瞭解](https://docs.microsoft.com/azure/azure-monitor/app/pricing#estimating-the-costs-to-manage-your-application)如何估算應用程式洞察的成本。

### <a name="understanding-your-usage-and-estimated-costs"></a>瞭解您的使用方式和估計成本

使用 Azure 監視器後，瞭解並跟蹤使用方式非常重要，並且有豐富的工具集可以促進此用。 

Azure 在[Azure 成本管理和計費](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis?toc=/azure/billing/TOC.json)中心中提供了大量有用的功能。 打開**Azure 成本管理 + 計費**中心後，按一下**成本管理**並選擇["範圍](https://docs.microsoft.com/azure/cost-management/understand-work-scopes)"（要調查的資源集）。 

然後，要查看過去 30 天的 Azure 監視器成本，請按一下 **"每日成本"** 磁貼，在"相對日期"下選擇"最近 30 天"，然後添加一個篩選器，選擇服務名稱：

1. Azure 監視器
2. Application Insights
3. Log Analytics
4. 深入解析與分析

這將導致一個視圖，例如：

![Azure 成本管理螢幕截圖](./media/usage-estimated-costs/010.png)

從這裡，您可以鑽取此累積成本摘要，在"按資源的成本"視圖中獲取更精細的詳細資訊。 在當前定價層中，Azure 日誌資料是在同一組儀錶上收費的，無論這些資料來源自日誌分析還是應用程式見解。 要將成本與日誌分析或應用程式見解使用方式分開，可以在**資源類型**上添加篩選器。 要查看所有應用程式見解成本，請將資源類型篩選為"Microsoft.insights/元件"，對於日誌分析成本，將資源類型篩選為"microsoft.運營見解/工作區"。 

通過[從 Azure 門戶下載使用方式，](https://docs.microsoft.com/azure/billing/billing-download-azure-invoice-daily-usage-date#download-usage-in-azure-portal)可以獲取更多使用的詳細資訊。 在下載的試算表中，每天可以看到每個 Azure 資源的使用方式。 在此 Excel 試算表中，可以通過首先在"儀錶類別"列上篩選以顯示"應用程式見解"和"日誌分析"，然後在"實例 ID"列上添加篩選器（即"包含 Microsoft.insights/元件"）中找到應用程式見解資源的使用方式。  大多數應用程式見解使用方式都報告在具有日誌分析儀錶類別的儀錶上，因為所有 Azure 監視器元件都有一個日誌後端。  只有舊版定價層和多步驟 Web 測試的應用程式見解資源報告應用程式見解表類別的應用程式見解。  用法顯示在"消耗量"列中，每個條目的單位顯示在"度量單位"列中。  有關詳細資訊，可説明您瞭解 Microsoft [Azure 帳單](https://docs.microsoft.com/azure/billing/billing-understand-your-bill)。 

> [!NOTE]
> 在**Azure 成本管理和計費**中心中使用**成本管理**是廣泛瞭解監視成本的首選方法。  [日誌分析和](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understand-your-usage-and-estimate-costs)[應用程式見解](https://docs.microsoft.com/azure/azure-monitor/app/pricing#understand-your-usage-and-estimate-costs)**的使用方式和估計成本**體驗為 Azure 監視器的每個部分提供了更深入的見解。

查看 Azure 監視器使用方式的另一個選項是"監視器中心**中的使用方式和估計成本**"頁。 這顯示了核心監視功能（如[警報、指標、通知](https://azure.microsoft.com/pricing/details/monitor/)[、Azure 日誌分析和](https://azure.microsoft.com/pricing/details/log-analytics/) [Azure 應用程式見解](https://azure.microsoft.com/pricing/details/application-insights/)）的使用。 客戶若是採用在 2018 年 4 月前提供的定價方案，則也會提供透過深入解析與分析供應項目購買的 Log Analytics 使用量。

在此頁面上，使用者可以檢視他們在過去 31 天內對這些資源的使用量 (依個別訂用帳戶彙總)。 `Drill-ins`顯示 31 天的使用趨勢。 系統必須彙整許多資料才能產生此預估值，因此請耐心等候頁面載入。

下列範例顯示監視使用量和估計最終成本的情形：

![使用量和估計成本入口網站螢幕擷取畫面](./media/usage-estimated-costs/001.png)

選取 [每月使用量] 資料行中的連結以開啟一個圖表，顯示過去 31 天的使用量趨勢： 

![每個節點包含長條圖的螢幕擷取畫面](./media/usage-estimated-costs/002.png)

## <a name="operations-management-suite-subscription-entitlements"></a>操作管理套件訂閱許可權

凡購買 Microsoft Operations Management Suite E1 和 E2 的客戶，皆符合資格使用 [Log Analytics](https://www.microsoft.com/cloud-platform/operations-management-suite) 和 [Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-pricing) 的按節點資料擷取權利。 若要以指定訂用帳戶享用 Log Analytics 工作區或 Application Insights 資源的這些權利： 

- Log Analytics 工作區應該使用「每個節點 (OMS)」定價層。
- 應用程式見解資源應使用"企業"定價層。

根據組織購買的套件的節點數，將某些訂閱移動到即用即付 （每 GB） 定價層可能有利，但這需要仔細考慮。

> [!WARNING]
> 如果您的組織具有當前的 Microsoft 運營管理套件 E1 和 E2，則通常最好將日誌分析工作區保留在"每個節點 （OMS）"定價層中，並將應用程式見解資源保留在"企業"定價層中。 
>
