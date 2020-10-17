---
title: 服務提供者的 Azure 監視器記錄 |Microsoft Docs
description: Azure 監視器記錄可協助管理服務提供者 (Msp) 、大型企業、獨立軟體廠商 (Isv) 和主機服務提供者會管理和監視客戶的內部部署或雲端基礎結構中的伺服器。
ms.subservice: logs
ms.topic: conceptual
author: MeirMen
ms.author: meirm
ms.date: 02/03/2020
ms.openlocfilehash: d92dd191c8bfe590f6dab392ff679e5d7712ae6c
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/17/2020
ms.locfileid: "92143851"
---
# <a name="azure-monitor-logs-for-service-providers"></a>服務提供者的 Azure 監視器記錄

Azure 監視器中的 Log Analytics 工作區可協助管理服務提供者 (Msp) 、大型企業、獨立軟體廠商 (Isv) 和主機服務提供者管理和監視客戶的內部部署或雲端基礎結構中的伺服器。

大型企業與服務提供者有許多相似之處，特別是當有集中式的 IT 團隊負責管理許多不同業務單位的 IT 時。 為了簡單起見，本文件會使用「服務提供者」** 這個詞彙，但是相同的功能也適用於企業或其他客戶。

針對屬於 [雲端解決方案提供者 (CSP) ](https://partner.microsoft.com/membership/cloud-solution-provider) 計畫一部分的合作夥伴和服務提供者，Azure 監視器中的 Log Analytics 是 Azure CSP 訂用帳戶中可用的其中一個 Azure 服務。

Azure 監視器中的 Log Analytics 也可供服務提供者透過 [Azure Lighthouse](../../lighthouse/overview.md)中的 Azure 委派資源管理功能來管理客戶資源。

## <a name="architectures-for-service-providers"></a>服務提供者的架構

Log Analytics 工作區提供一個方法，讓系統管理員控制 [記錄](data-platform-logs.md) 資料的流程和隔離，以及建立可解決其特定商務需求的架構。 [本文說明工作](design-logs-deployment.md) 區的設計、部署和遷移考慮，以及 [管理存取](manage-access.md) 文章會討論如何套用和記錄管理資料的許可權。 服務提供者會有其他考量。

服務提供者對於 Log Analytics 工作區有三個可能的架構：

### <a name="1-distributed---logs-are-stored-in-workspaces-located-in-the-customers-tenant"></a>1. 分散式記錄會儲存在位於客戶租使用者中的工作區

在此架構中，會在用於該客戶所有記錄的客戶租用戶中部署工作區。

有兩種方式可讓服務提供者系統管理員取得客戶租使用者中 Log Analytics 工作區的存取權：

- 客戶可以將個別使用者從服務提供者新增為 [ (B2B) 的 Azure Active Directory 來賓使用者 ](../../active-directory/external-identities/what-is-b2b.md)。 服務提供者系統管理員必須登入 Azure 入口網站中的每個客戶目錄，才能存取這些工作區。 這也需要客戶為每個服務提供者系統管理員管理個別的存取權。
- 為了提供更大的擴充性和彈性，服務提供者可以使用[Azure Lighthouse](../../lighthouse/overview.md)的[Azure 委派資源管理](../../lighthouse/concepts/azure-delegated-resource-management.md)功能來存取客戶的租使用者。 使用這個方法時，服務提供者系統管理員會包含在服務提供者租使用者的 Azure AD 使用者群組中，而且會在每個客戶的上執行緒序期間，將存取權授與此群組。 然後，這些系統管理員可以從自己的服務提供者租使用者中存取每個客戶的工作區，而不需要個別登入每個客戶的租使用者。 以這種方式存取客戶的 Log Analytics 工作區資源，可減少用戶端所需的工作，並可讓您更輕鬆地透過 [Azure 監視器活頁簿](./workbooks-overview.md)之類的工具，在相同服務提供者所管理的多個客戶上收集及分析資料。 如需詳細資訊，請參閱 [大規模監視客戶資源](../../lighthouse/how-to/monitor-at-scale.md)。

分散式架構的優點如下：

* 客戶可以透過 [azure 委派的資源管理](../../lighthouse/concepts/azure-delegated-resource-management.md)來確認特定層級的許可權，也可以使用自己的 [azure 角色型存取控制 (Azure RBAC) ](../../role-based-access-control/overview.md)來管理對記錄的存取。
* 您可以從所有類型的資源收集記錄，而不只是以代理程式為基礎的 VM 資料。 例如，Azure 稽核記錄。
* 每個客戶對於其工作區都可以有不同的設定，例如保留和資料限定。
* 基於法規和合規性而隔離客戶。
* 每個工作區的費用都將會累計到客戶的訂用帳戶中。

分散式架構的缺點如下：

* 使用 Azure 監視器活頁簿等工具集中視覺化及分析客戶租使用者的資料，可能會導致更慢的體驗，特別是在分析超過50個以上工作區的資料時。
* 如果客戶未針對 Azure 委派的資源管理上線，服務提供者系統管理員必須布建在客戶目錄中，而且服務提供者會很難一次管理大量客戶租使用者。

### <a name="2-central---logs-are-stored-in-a-workspace-located-in-the-service-provider-tenant"></a>2. 中央-記錄會儲存在位於服務提供者租使用者中的工作區

在此架構中，記錄不會儲存在客戶的租用戶中，但只會儲存在其中一個服務提供者訂用帳戶內的集中位置。 客戶 VM 上安裝的代理程式會設定為使用工作區識別碼和祕密金鑰以傳送記錄至此工作區。

集中式架構的優點如下：

* 容易管理大量客戶，並將其整合至各種後端系統。
* 服務提供者對於記錄和各種成品 (例如函式和儲存的查詢) 都具備完整的擁有權。
* 服務提供者可以跨其所有客戶執行分析。

集中式架構的缺點如下：

* 此架構僅適用於代理程式型 VM 資料，不涵蓋 PaaS、SaaS 與 Azure 網狀架構資料來源。
* 當客戶的資料合併到單一工作區時，區隔彼此的資料可能會很困難。 唯一的好方法是使用電腦的完整網域名稱 (FQDN) 或透過 Azure 訂用帳戶識別碼。
* 來自所有客戶的所有資料都會儲存在相同的區域中，帳單只有一份，而且有相同的保留和組態設定。
* Azure 網狀架構和 PaaS 服務 (例如 Azure 診斷和 Azure 稽核記錄) 會要求工作區位於與資源相同的租用戶中，因此無法將記錄傳送至集中式工作區。
* 來自所有客戶的所有 VM 代理程式都會使用相同的工作區識別碼和金鑰，向集中式工作區進行驗證。 在不中斷其他客戶的情況下，沒有任何方法可以封鎖來自特定客戶的記錄。

### <a name="3-hybrid---logs-are-stored-in-workspace-located-in-the-customers-tenant-and-some-of-them-are-pulled-to-a-central-location"></a>3. 混合式記錄會儲存在客戶租使用者中的工作區，而其中有些會提取到中央位置。

第三個架構是兩個選項的混合。 這個架構是以第一個分散式架構為基礎，其中記錄儲存在每個客戶的本機，但使用特定機制來建立記錄的集中存放區。 一部分的記錄會提取到用於報告和分析的集中位置。 這個部分可能是少量的資料類型或活動摘要 (例如每日統計資料)。

有兩個選項可在中央位置執行記錄：

1. 集中式工作區：服務提供者可以在其租用戶中建立工作區，並使用可搭配[資料收集 API](./data-collector-api.md) 使用[查詢 API](https://dev.loganalytics.io/) 的指令碼，將不同工作區的資料帶到這個集中位置。 指令碼以外的另一個選項是使用 [Azure Logic Apps](../../logic-apps/logic-apps-overview.md)。

2. Power BI 作為集中位置：當各種工作區使用 Log Analytics 工作區和 [Power BI](./powerbi.md)之間的整合將資料匯出至該位置時，Power BI 可以作為中心位置。

## <a name="next-steps"></a>後續步驟

* 使用 [Resource Manager 範本](../samples/resource-manager-workspace.md)建立和設定工作區

* 使用 [PowerShell](./powershell-workspace-configuration.md)自動建立工作區

* 使用[警示](./alerts-overview.md)與現有的系統整合

* 使用 [Power BI](./powerbi.md) 來產生摘要報告

* 將客戶上線至 [Azure 委派的資源管理](../../lighthouse/concepts/azure-delegated-resource-management.md)。