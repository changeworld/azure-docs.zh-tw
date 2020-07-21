---
title: 跨工作區和租使用者擴充 Azure Sentinel |Microsoft Docs
description: 如何使用多個租使用者來 Azure Sentinel MSSP 服務提供者。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/11/2020
ms.author: yelevin
ms.openlocfilehash: 596d0f4870d9331a332dfb81bd7d2d224964a593
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86519008"
---
# <a name="extend-azure-sentinel-across-workspaces-and-tenants"></a>跨工作區和租用戶擴充 Azure Sentinel

## <a name="the-need-to-use-multiple-azure-sentinel-workspaces"></a>使用多個 Azure Sentinel 工作區的需求

Azure Sentinel 建置於 Log Analytics 工作區之上。 您會發現上架 Azure Sentinel 的第一個步驟是選取您想要用於該用途的 Log Analytics 工作區。

使用單一工作區時，您可以獲得 Azure Sentinel 體驗的完整優點。 儘管如此，在某些情況下，您可能需要有多個工作區。 下表列出其中一些情況，並在可能的情況下，建議如何滿足單一工作區的需求：

| 需求 | 描述 | 減少工作區計數的方法 |
|-------------|-------------|--------------------------------|
| 主權與法規遵循 | 工作區會系結至特定區域。 如果資料需要保留在不同的[Azure 地理](https://azure.microsoft.com/global-infrastructure/geographies/)位置以滿足法規需求，則必須將其分割為不同的工作區。 |  |
| 資料擁有權 | 資料擁有權（例如，子公司或附屬公司）的界限，會使用個別的工作區進行更精確的描繪。 |  |
| 多個 Azure 租使用者 | Azure Sentinel 僅支援從 Microsoft 和 Azure SaaS 資源在自己的 Azure Active Directory （Azure AD）租使用者界限內收集資料。 因此，每個 Azure AD 租使用者都需要個別的工作區。 |  |
| 細微的資料存取控制 | 組織可能需要允許組織內部或外部的不同群組存取 Azure Sentinel 所收集的部分資料。 例如：<br><ul><li>資源擁有者存取其資源的相關資料</li><li>地區或子公司 Soc 的存取權，可存取其組織各部分的相關資料</li></ul> | 使用[資源 rbac](https://techcommunity.microsoft.com/t5/azure-sentinel/controlling-access-to-azure-sentinel-data-resource-rbac/ba-p/1301463)或[資料表層級 rbac](https://techcommunity.microsoft.com/t5/azure-sentinel/table-level-rbac-in-azure-sentinel/ba-p/965043) |
| 細微保留設定 | 在過去，有多個工作區是為不同的資料類型設定不同保留週期的唯一方法。 這在許多情況下都不需要這麼做，因為它會引進資料表層級的保留設定。 | 使用[資料表層級保留設定](https://techcommunity.microsoft.com/t5/azure-sentinel/new-per-data-type-retention-is-now-available-for-azure-sentinel/ba-p/917316)或將[資料刪除](../azure-monitor/platform/personal-data-mgmt.md#how-to-export-and-delete-private-data)自動化 |
| 分割計費 | 藉由將工作區放在不同的訂用帳戶中，可以向不同的合作物件收費。 | 使用量報告和交叉收費 |
| 舊版架構 | 使用多個工作區可能源自于歷程記錄設計，這會考慮到不會有任何事實的限制或最佳做法。 它也可能是任意的設計選擇，可加以修改以更符合 Azure Sentinel。<br><br>範例包括：<br><ul><li>部署時使用每個訂用帳戶預設工作區 Azure 資訊安全中心</li><li>細微存取控制或保留設定的需求，這是相對較新的解決方案</li></ul> | 重新架構工作區 |

### <a name="managed-security-service-provider-mssp"></a>受控安全性服務提供者（MSSP）

特定使用案例會強制執行多個工作區，這是一個 MSSP Azure Sentinel 服務。 在此情況下，許多情況下，如果不符合上述所有需求，則會在租使用者中建立多個工作區，這是最佳做法。 MSSP 可以使用[Azure 燈塔](../lighthouse/overview.md)來擴充跨租使用者的 Azure Sentinel 跨工作區功能。

## <a name="azure-sentinel-multiple-workspace-architecture"></a>Azure Sentinel 多個工作區架構

如上述需求所暗示，在某些情況下，多個 Azure Sentinel 工作區（可能跨 Azure Active Directory （Azure AD）租使用者）必須由單一 SOC 集中監視和管理。

- MSSP Azure Sentinel 服務。

- 提供多個子公司的全域 SOC，每一個都有自己的本機 SOC。

- SOC 監視組織內的多個 Azure AD 租使用者。

為了解決這項需求，Azure Sentinel 提供多個工作區的功能，可啟用集中監視、設定和管理，在 SOC 涵蓋的所有專案上提供單一的玻璃窗格，如下圖所示。

:::image type="content" source="media/extend-sentinel-across-workspaces-tenants/cross-workspace-architecture.png" alt-text="跨工作區架構":::

此模型透過完全集中式的模型提供顯著的優點，其中所有資料都會複製到單一工作區：

- 彈性的角色指派給全域和本機 Soc，或 MSSP 其客戶。

- 資料擁有權、資料隱私權和法規遵循方面的挑戰較少。

- 最少的網路延遲和費用。

- 輕鬆上架和脫離新的子公司或客戶。

在下列各節中，我們將說明如何操作此模型，特別是如何：

- 集中監視多個工作區（可能跨租使用者），並以單一窗格提供 SOC。

- 使用自動化，集中設定和管理多個工作區（可能跨租使用者）。

## <a name="cross-workspace-monitoring"></a>跨工作區監視

### <a name="manage-incidents-on-multiple-workspaces"></a>管理多個工作區上的事件

Azure Sentinel 支援[多個工作區事件檢視](./multiple-workspace-view.md)，可促進跨多個工作區的中央事件監控和管理。 集中式事件檢視可讓您直接管理事件，或在原始工作區的內容中，以透明方式向下切入事件詳細資料。

### <a name="cross-workspace-querying"></a>跨工作區查詢

Azure Sentinel 支援[在單一查詢中查詢多個工作區](../azure-monitor/log-query/cross-workspace-query.md)，可讓您在單一查詢中搜尋和相互關聯多個工作區中的資料。 

- 使用[workspace （）運算式](../azure-monitor/log-query/workspace-expression.md)來參考不同工作區中的資料表。 
- 使用[union 運算子](https://docs.microsoft.com/azure/data-explorer/kusto/query/unionoperator?pivots=azuremonitor)搭配 workspace （）運算式，在多個工作區的資料表之間套用查詢。

您可以[使用已儲存的函](../azure-monitor/log-query/functions.md)式來簡化跨工作區的查詢。 例如，如果工作區的參考很長，您可能會想要將運算式儲存為名為的函式 `workspace("customer-A's-hard-to-remember-workspace-name").SecurityEvent` `SecurityEventCustomerA` 。 然後，您可以將查詢撰寫為 `SecurityEventCustomerA | where ...` 。

函數也可以簡化常用的聯集。 例如，您可以將下列運算式儲存為名為的函式 `unionSecurityEvent` ：

`union workspace(“hard-to-remember-workspace-name-1”).SecurityEvent, workspace(“hard-to-remember-workspace-name-2”).SecurityEvent`

然後，您可以從開始，在這兩個工作區中撰寫查詢 `unionSecurityEvent | where ...` 。

> [!NOTE] 
> 在相同查詢中查詢多個工作區可能會影響效能，因此只有在邏輯需要這項功能時才建議使用。

### <a name="using-cross-workspace-workbooks"></a>使用跨工作區的活頁簿

活頁[簿](./overview.md#workbooks)提供儀表板和應用程式來 Azure Sentinel。 使用多個工作區時，會跨工作區提供監視和動作。

活頁簿可以在三種方法中提供跨工作區查詢，每個方法都符合不同層級的使用者專業知識：

| 方法  | 描述 | 何時應該使用？ |
|---------|-------------|--------------------|
| 撰寫跨工作區查詢 | 活頁簿建立者可以在活頁簿中撰寫跨工作區的查詢（如上所述）。 | 此選項可讓活頁簿建立者完全從工作區結構保護使用者。 |
| 將工作區選取器新增至活頁簿 | 活頁簿建立者可以將工作區選取器當做活頁簿的一部分來執行，如[這裡](https://techcommunity.microsoft.com/t5/azure-sentinel/making-your-azure-sentinel-workbooks-multi-tenant-or-multi/ba-p/1402357)所述。 | 此選項可讓使用者控制活頁簿所顯示的工作區，方法是使用簡單易用的下拉式清單方塊。 |
| 以互動方式編輯活頁簿 | 修改現有活頁簿的「高級使用者」可以編輯其中的查詢，並使用編輯器中的工作區選取器來選取目標工作區。 | 此選項可讓 power 使用者輕鬆地修改現有的活頁簿，以使用多個工作區。 |
|

### <a name="cross-workspace-hunting"></a>跨工作區搜尋

Azure Sentinel 提供預先載入的查詢範例，旨在協助您開始使用，並熟悉資料表和查詢語言。 這些內建的搜尋查詢是由 Microsoft 資訊安全研究人員連續開發，包括新增查詢和微調現有的查詢，以提供您尋找新偵測的進入點，並找出您的安全性工具可能無法偵測到的入侵跡象。  

跨工作區的搜尋功能可讓您的威脅獵人及使用 union 運算子和 workspace （）運算式，來建立新的搜尋查詢，或調整現有的尋找查詢，以涵蓋多個工作區，如上面所示。

## <a name="cross-workspace-management-using-automation"></a>使用自動化進行跨工作區管理

若要設定和管理多個 Azure Sentinel 工作區，您必須將 Azure Sentinel 管理 API 的使用自動化。 如需如何自動化部署 Azure Sentinel 資源的詳細資訊，包括警示規則、搜尋查詢、活頁簿和操作手冊，請參閱[擴充 Azure Sentinel： api、整合和管理自動化](https://techcommunity.microsoft.com/t5/azure-sentinel/extending-azure-sentinel-apis-integration-and-management/ba-p/1116885)。

另請參閱以程式[代碼的方式部署和管理 Azure Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/deploying-and-managing-azure-sentinel-as-code/ba-p/1131928) ，並[結合 Azure 燈塔與 Sentinel 的 DevOps 功能](https://techcommunity.microsoft.com/t5/azure-sentinel/combining-azure-lighthouse-with-sentinel-s-devops-capabilities/ba-p/1210966)，以取得可供管理程式碼 Azure Sentinel 的整合、以社區為主的方法，以及從私人 GitHub 存放庫部署和設定資源。 


## <a name="whats-not-supported-across-workspaces"></a>跨工作區不支援哪些功能？

跨工作區不支援下列功能：

- 排程的警示規則無法使用跨工作區查詢跨工作區執行。

## <a name="managing-workspaces-across-tenants-using-azure-lighthouse"></a>使用 Azure 燈塔管理跨租使用者的工作區

如前所述，在許多情況下，不同的 Azure Sentinel 工作區可以位於不同的 Azure AD 租使用者中。 您可以使用[Azure 燈塔](../lighthouse/overview.md)來擴充跨租使用者界限的所有跨工作區活動，讓您的管理租使用者中的使用者可以在所有租使用者的 Azure Sentinel 工作區上工作。 Azure 燈塔[上架](../lighthouse/how-to/onboard-customer.md)之後，請使用 Azure 入口網站上的 [[目錄 + 訂](./multiple-tenants-service-providers.md#how-to-access-azure-sentinel-from-other-tenants)用帳戶] 選取器來選取包含您要管理之工作區的所有訂用帳戶，以確保所有訂用帳戶都可以在入口網站的不同工作區選取器中使用。

使用 Azure 燈塔時，建議您為每個 Azure Sentinel 角色建立一個群組，並將每個租使用者的許可權委派給這些群組。

## <a name="next-steps"></a>後續步驟
在本檔中，您已瞭解如何在多個工作區和租使用者之間擴充 Azure Sentinel 的功能。 如需有關執行 Azure Sentinel 跨工作區架構的實際指引，請參閱下列文章：

- 瞭解如何使用 Azure 燈塔在 Azure Sentinel 中使用[多個](./multiple-tenants-service-providers.md)租使用者。
- 瞭解如何順暢地[查看和管理多個工作區中的事件](./multiple-workspace-view.md)。