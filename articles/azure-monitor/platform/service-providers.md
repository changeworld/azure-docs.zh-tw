---
title: 服務提供者的 Azure 監視器日誌 |微軟文檔
description: Azure 監視器日誌可説明託管服務提供者 （MSP）、大型企業、獨立軟體廠商 （ISV） 和託管服務提供者管理和監視客戶本地或雲基礎結構中的伺服器。
ms.subservice: logs
ms.topic: conceptual
author: MeirMen
ms.author: meirm
ms.date: 02/03/2020
ms.openlocfilehash: ed398e12ee90f2eef2cfa78e2ed02701e6012517
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658875"
---
# <a name="azure-monitor-logs-for-service-providers"></a>服務提供者的 Azure 監視器日誌

Azure 監視器中的日誌分析工作區可説明託管服務提供者 （MSP）、大型企業、獨立軟體廠商 （ISV） 和託管服務提供者管理和監視客戶本地或雲基礎結構中的伺服器。

大型企業與服務提供者有許多相似之處，特別是當有集中式的 IT 團隊負責管理許多不同業務單位的 IT 時。 為了簡單起見，本文件會使用「服務提供者」** 這個詞彙，但是相同的功能也適用於企業或其他客戶。

對於屬於[雲解決方案供應商 （CSP）](https://partner.microsoft.com/Solutions/cloud-reseller-overview)計畫的合作夥伴和服務提供者，Azure 監視器中的日誌分析是 Azure CSP 訂閱中可用的 Azure 服務之一。

Azure 監視器中的日誌分析也可以由管理客戶資源的服務提供者使用，這些服務提供程式可以通過[Azure 燈塔](https://docs.microsoft.com/azure/lighthouse/overview)中的 Azure 委派資源管理功能。

## <a name="architectures-for-service-providers"></a>服務提供者的架構

日誌分析工作區為管理員提供了一種控制[日誌](data-platform-logs.md)資料的流和隔離以及創建滿足其特定業務需求的體系結構的方法。 [本文](design-logs-deployment.md)介紹工作區的設計、部署和遷移注意事項，[管理訪問](manage-access.md)文章討論了如何應用和記錄管理資料的許可權。 服務提供者會有其他考量。

服務提供者對於 Log Analytics 工作區有三個可能的架構：

### <a name="1-distributed---logs-are-stored-in-workspaces-located-in-the-customers-tenant"></a>1. 分散式 - 日誌存儲在位於客戶租戶中的工作區中

在此架構中，會在用於該客戶所有記錄的客戶租用戶中部署工作區。

服務提供者管理員可通過兩種方式訪問客戶租戶中的日誌分析工作區：

- 客戶可以將服務提供者中的單個使用者添加為 Azure[活動目錄來賓使用者 （B2B）。](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b) 服務提供者管理員必須登錄到 Azure 門戶中的每個客戶的目錄才能訪問這些工作區。 這還需要客戶管理每個服務提供者管理員的單獨訪問。
- 為了增強可擴充性和靈活性，服務提供者可以使用[Azure 燈塔](https://docs.microsoft.com/azure/lighthouse/overview)的[Azure 委派資源管理](https://docs.microsoft.com/azure/lighthouse/concepts/azure-delegated-resource-management)功能訪問客戶的租戶。 使用此方法，服務提供者管理員包含在服務提供者的租戶中的 Azure AD 使用者組中，並且在每個客戶的入職過程中授予此組存取權限。 然後，這些管理員可以從自己的服務提供者租戶中訪問每個客戶的工作區，而不必單獨登錄到每個客戶的租戶。 以這種方式訪問客戶的日誌分析工作區資源可減少客戶方面所需的工作，並可通過[Azure 監視器活頁簿](https://docs.microsoft.com/azure//azure-monitor/platform/workbooks-overview)等工具更輕鬆地跨同一服務提供者管理的多個客戶收集和分析資料。 有關詳細資訊，請參閱[大規模監視客戶資源](https://docs.microsoft.com/azure/lighthouse/how-to/monitor-at-scale)。

分散式體系結構的優點是：

* 客戶可以通過[Azure 委派的資源管理](https://docs.microsoft.com/azure/lighthouse/concepts/azure-delegated-resource-management)確認特定級別的許可權，也可以使用他們自己的[基於角色的訪問](https://docs.microsoft.com/azure/role-based-access-control/overview)來管理對日誌的訪問。
* 可以從所有類型的資源（而不僅僅是基於代理的 VM 資料）收集日誌。 例如，Azure 稽核記錄。
* 每個客戶對於其工作區都可以有不同的設定，例如保留和資料限定。
* 基於法規和合規性而隔離客戶。
* 每個工作區的費用都將會累計到客戶的訂用帳戶中。

分散式體系結構的缺點是：

* 使用 Azure 監視器活頁簿 等工具對客戶租戶進行集中視覺化和分析資料可能會導致體驗變慢，尤其是在分析 50 多個工作區中的資料時。
* 如果客戶未加入 Azure 委派資源管理，則必須在客戶目錄中預配服務提供者管理員，並且服務提供者很難同時管理大量客戶租戶。

### <a name="2-central---logs-are-stored-in-a-workspace-located-in-the-service-provider-tenant"></a>2. 中央 - 日誌存儲在位於服務提供者租戶的工作區中

在此架構中，記錄不會儲存在客戶的租用戶中，但只會儲存在其中一個服務提供者訂用帳戶內的集中位置。 客戶 VM 上安裝的代理程式會設定為使用工作區識別碼和祕密金鑰以傳送記錄至此工作區。

集中式體系結構的優點是：

* 容易管理大量客戶，並將其整合至各種後端系統。
* 服務提供者對於記錄和各種成品 (例如函式和儲存的查詢) 都具備完整的擁有權。
* 服務提供者可以跨其所有客戶執行分析。

集中式體系結構的缺點是：

* 此架構僅適用於代理程式型 VM 資料，不涵蓋 PaaS、SaaS 與 Azure 網狀架構資料來源。
* 當客戶的資料合併到單一工作區時，區隔彼此的資料可能會很困難。 唯一的好方法是使用電腦的完整網域名稱 (FQDN) 或透過 Azure 訂用帳戶識別碼。 
* 來自所有客戶的所有資料都會儲存在相同的區域中，帳單只有一份，而且有相同的保留和組態設定。
* Azure 網狀架構和 PaaS 服務 (例如 Azure 診斷和 Azure 稽核記錄) 會要求工作區位於與資源相同的租用戶中，因此無法將記錄傳送至集中式工作區。
* 來自所有客戶的所有 VM 代理程式都會使用相同的工作區識別碼和金鑰，向集中式工作區進行驗證。 在不中斷其他客戶的情況下，沒有任何方法可以封鎖來自特定客戶的記錄。

### <a name="3-hybrid---logs-are-stored-in-workspace-located-in-the-customers-tenant-and-some-of-them-are-pulled-to-a-central-location"></a>3. 混合日誌存儲在位於客戶租戶的工作區中，其中一些日誌被拉到中心位置。

第三個架構是兩個選項的混合。 這個架構是以第一個分散式架構為基礎，其中記錄儲存在每個客戶的本機，但使用特定機制來建立記錄的集中存放區。 一部分的記錄會提取到用於報告和分析的集中位置。 這個部分可能是少量的資料類型或活動摘要 (例如每日統計資料)。

在中心位置實現日誌有兩個選項：

1. 集中式工作區：服務提供者可以在其租用戶中建立工作區，並使用可搭配[資料收集 API](../../azure-monitor/platform/data-collector-api.md) 使用[查詢 API](https://dev.loganalytics.io/) 的指令碼，將不同工作區的資料帶到這個集中位置。 指令碼以外的另一個選項是使用 [Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)。

2. Power BI 作為中心位置：當各種工作區使用日誌分析工作區和[Power BI](../../azure-monitor/platform/powerbi.md)之間的集成將資料匯出到它時，Power BI 可以充當中心位置。 

## <a name="next-steps"></a>後續步驟

* 使用 [Resource Manager 範本](template-workspace-configuration.md)建立和設定工作區

* 使用 [PowerShell](../../azure-monitor/platform/powershell-workspace-configuration.md)自動建立工作區 

* 使用[警示](../../azure-monitor/platform/alerts-overview.md)與現有的系統整合

* 使用 [Power BI](../../azure-monitor/platform/powerbi.md) 來產生摘要報告

* 板載客戶到[Azure 委派的資源管理](https://docs.microsoft.com/azure/lighthouse/concepts/azure-delegated-resource-management)。
