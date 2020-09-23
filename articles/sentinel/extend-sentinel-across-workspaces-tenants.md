---
title: 跨工作區和租使用者延伸 Azure Sentinel |Microsoft Docs
description: 如何使用 Azure Sentinel 來查詢及分析跨工作區和租使用者的資料。
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
ms.date: 09/11/2020
ms.author: yelevin
ms.openlocfilehash: b899069a03b39d068f2b4059cf26d3baf1f3beae
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90905414"
---
# <a name="extend-azure-sentinel-across-workspaces-and-tenants"></a>跨工作區和租用戶擴充 Azure Sentinel

## <a name="the-need-to-use-multiple-azure-sentinel-workspaces"></a>需要使用多個 Azure Sentinel 工作區

Azure Sentinel 是以 Log Analytics 工作區為基礎。 您將會注意到上架 Azure Sentinel 的第一個步驟是選取您想要用於該用途的 Log Analytics 工作區。

使用單一工作區時，您可以獲得 Azure Sentinel 體驗的完整權益。 儘管如此，在某些情況下，您可能需要有多個工作區。 下表列出其中一些情況，而且可能的話，建議使用單一工作區來滿足需求的方式：

| 需求 | 描述 | 減少工作區計數的方法 |
|-------------|-------------|--------------------------------|
| 主權和法規合規性 | 工作區會系結至特定區域。 如果資料需要保存在不同的 [Azure 地理](https://azure.microsoft.com/global-infrastructure/geographies/) 位置以滿足法規需求，則必須將其分割成不同的工作區。 |  |
| 資料擁有權 | 資料擁有權的界限（例如，分公司或附屬公司）更能使用不同的工作區來描繪。 |  |
| 多個 Azure 租使用者 | Azure Sentinel 僅支援在自己的 Azure Active Directory (Azure AD) 租使用者界限內，從 Microsoft 和 Azure SaaS 資源收集資料。 因此，每個 Azure AD 租使用者都需要個別的工作區。 |  |
| 細微資料存取控制 | 組織可能需要允許組織內部或外部的不同群組，以存取 Azure Sentinel 所收集的部分資料。 例如：<br><ul><li>資源擁有者對其資源相關資料的存取權</li><li>地區或子公司 SOCs 存取其組織部分的相關資料</li></ul> | 使用 [資源 rbac](https://techcommunity.microsoft.com/t5/azure-sentinel/controlling-access-to-azure-sentinel-data-resource-rbac/ba-p/1301463) 或 [資料表層級 rbac](https://techcommunity.microsoft.com/t5/azure-sentinel/table-level-rbac-in-azure-sentinel/ba-p/965043) |
| 細微保留設定 | 在過去，有多個工作區是針對不同的資料類型設定不同保留期限的唯一方式。 由於資料表層級保留設定的引入，在許多情況下，已不再需要這項功能。 | 使用 [資料表層級保留設定](https://techcommunity.microsoft.com/t5/azure-sentinel/new-per-data-type-retention-is-now-available-for-azure-sentinel/ba-p/917316) 或自動化 [資料刪除](../azure-monitor/platform/personal-data-mgmt.md#how-to-export-and-delete-private-data) |
| 分割計費 | 藉由將工作區放在不同的訂用帳戶中，就可以向不同的合作物件收費。 | 使用方式報告和交叉收費 |
| 舊版架構 | 使用多個工作區可能源自于考慮限制的歷程記錄設計，或是不再成立的最佳作法。 它也可能是任意的設計選擇，可進行修改以更妥善配合 Azure Sentinel。<br><br>範例包括：<br><ul><li>部署 Azure 資訊安全中心時使用每個訂用帳戶預設工作區</li><li>需要細微的存取控制或保留設定，這是相對較新的解決方案</li></ul> | 重新架構工作區 |

### <a name="managed-security-service-provider-mssp"></a>受控安全性服務提供者 (MSSP) 

要求多個工作區的特定使用案例是 MSSP Azure Sentinel 服務。 在此情況下，許多情況下都不適用上述所有需求，而是在租使用者中建立多個工作區，這是最佳作法。 MSSP 可以使用 [Azure Lighthouse](../lighthouse/overview.md) 來延伸跨租使用者的 Azure Sentinel 跨工作區功能。

## <a name="azure-sentinel-multiple-workspace-architecture"></a>Azure Sentinel 多個工作區架構

如同上述需求所暗示，在某些情況下，可能會有多個 Azure Sentinel 工作區（可能跨 Azure Active Directory (Azure AD) 租使用者）必須由單一 SOC 進行集中監視和管理。

- MSSP Azure Sentinel 服務。

- 提供多個子公司的全域 SOC，每個都有自己的本機 SOC。

- SOC 監視組織內的多個 Azure AD 租使用者。

為了滿足這項需求，Azure Sentinel 提供多個工作區的功能，可啟用集中監視、設定和管理，並在 SOC 所涵蓋的所有專案之間提供單一的透明窗格，如下圖所示。

:::image type="content" source="media/extend-sentinel-across-workspaces-tenants/cross-workspace-architecture.png" alt-text="跨工作區架構":::

此模型在完全集中式模型上提供顯著的優點，其中所有資料都會複製到單一工作區：

- 彈性的角色指派給 global 和 local SOCs，或 MSSP 其客戶。

- 資料擁有權、資料隱私權和法規合規性方面的挑戰較少。

- 減少網路延遲和費用。

- 讓新的子公司或客戶輕鬆上架和脫離。

在下列各節中，我們將說明如何操作此模型，尤其是如何：

- 集中監視多個工作區（可能跨租使用者），並以單一窗格提供 SOC。

- 使用自動化來集中設定和管理多個工作區（可能跨租使用者）。

## <a name="cross-workspace-monitoring"></a>跨工作區監視

### <a name="manage-incidents-on-multiple-workspaces"></a>管理多個工作區的事件

Azure Sentinel 支援 [多個工作區事件](./multiple-workspace-view.md) ，可促進跨多個工作區的中央事件監視和管理。 集中式事件檢視可讓您直接管理事件，或在原始工作區的內容中，以透明方式向下切入事件詳細資料。

### <a name="cross-workspace-querying"></a>跨工作區查詢

Azure Sentinel 支援 [在單一查詢中查詢多個工作區](../azure-monitor/log-query/cross-workspace-query.md)，可讓您在單一查詢中搜尋多個工作區的資料並使其相互關聯。 

- 使用 [工作區 ( # A1 運算式](../azure-monitor/log-query/workspace-expression.md) 來參考不同工作區中的資料表。 
- 使用 [union 運算子](https://docs.microsoft.com/azure/data-explorer/kusto/query/unionoperator?pivots=azuremonitor) 以及工作空間 ( # A1 運算式，在多個工作區的資料表之間套用查詢。

您可以 [使用已儲存的函](../azure-monitor/log-query/functions.md) 式來簡化跨工作區查詢。 例如，如果工作區的參考很長，您可能會想要將運算式儲存 `workspace("customer-A's-hard-to-remember-workspace-name").SecurityEvent` 為稱為的函式 `SecurityEventCustomerA` 。 然後您可以將查詢撰寫為 `SecurityEventCustomerA | where ...` 。

函數也可以簡化常用的聯集。 例如，您可以將下列運算式儲存為稱為的函式 `unionSecurityEvent` ：

`union workspace(“hard-to-remember-workspace-name-1”).SecurityEvent, workspace(“hard-to-remember-workspace-name-2”).SecurityEvent`

然後，您可以從開始，在這兩個工作區中撰寫查詢 `unionSecurityEvent | where ...` 。

#### <a name="scheduled-alerts"></a>排程的警示

跨工作區查詢現在可以包含在分析規則的排程警示中，但有下列限制：

- 單一查詢中最多可以包含10個工作區。
- 您必須在查詢中參考的每個工作區上部署 Azure Sentinel。

> [!NOTE] 
> 在相同的查詢中查詢多個工作區可能會影響效能，因此只有在邏輯需要這項功能時才建議使用。

### <a name="using-cross-workspace-workbooks"></a>使用跨工作區活頁簿

活頁[簿](./overview.md#workbooks)會提供要 Azure Sentinel 的儀表板和應用程式。 使用多個工作區時，它們會跨工作區提供監視和動作。

活頁簿可以使用三種方法的其中一種來提供跨工作區查詢，每個方法都能滿足不同層級的使用者專長：

| 方法  | 描述 | 我何時應該使用？ |
|---------|-------------|--------------------|
| 寫入跨工作區查詢 | 活頁簿建立者可以撰寫 () 在活頁簿中所述的跨工作區查詢。 | 此選項可讓活頁簿建立者完全從工作區結構保護使用者。 |
| 將工作區選取器新增至活頁簿 | 活頁簿建立者可以將工作區選取器實作為活頁簿的一部分[，如下所述。](https://techcommunity.microsoft.com/t5/azure-sentinel/making-your-azure-sentinel-workbooks-multi-tenant-or-multi/ba-p/1402357) | 此選項可讓使用者透過便於使用的下拉式方塊，控制活頁簿所顯示的工作區。 |
| 以互動方式編輯活頁簿 | 修改現有活頁簿的 advanced 使用者可以編輯其中的查詢，使用編輯器中的工作區選取器選取目標工作區。 | 此選項可讓 power 使用者輕鬆地修改現有的活頁簿，以使用多個工作區。 |
|

### <a name="cross-workspace-hunting"></a>跨工作區搜尋

Azure Sentinel 提供預先載入的查詢範例，以協助您開始使用，並讓您熟悉資料表和查詢語言。 這些內建的搜尋查詢是由 Microsoft 安全性研究人員持續開發的，這兩個方法都是在加入新的查詢和微調現有的查詢時，提供您尋找新偵測的進入點，並找出安全性工具可能未偵測到的入侵徵兆。  

跨工作區的搜尋功能可讓您的威脅獵人及建立新的搜尋查詢，或使用等位運算子和工作區 ( # A1 運算式（如上所示）來建立新的搜尋查詢或調整現有的搜尋查詢，以涵蓋多個工作區。

## <a name="cross-workspace-management-using-automation"></a>使用自動化的跨工作區管理

若要設定和管理多個 Azure Sentinel 工作區，您必須將 Azure Sentinel 管理 API 的使用自動化。 如需如何自動化部署 Azure Sentinel 資源的詳細資訊，包括警示規則、搜尋查詢、活頁簿和操作手冊，請參閱 [擴充 Azure Sentinel： api、整合和管理自動化](https://techcommunity.microsoft.com/t5/azure-sentinel/extending-azure-sentinel-apis-integration-and-management/ba-p/1116885)。

另請參閱以程式 [代碼的形式部署和管理 Azure Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/deploying-and-managing-azure-sentinel-as-code/ba-p/1131928) ，並將 [Azure Lighthouse 與 Sentinel 的 DevOps 功能結合](https://techcommunity.microsoft.com/t5/azure-sentinel/combining-azure-lighthouse-with-sentinel-s-devops-capabilities/ba-p/1210966) ，以進行管理 Azure Sentinel 即程式碼，以及從私人 GitHub 存放庫部署和設定資源。 

## <a name="managing-workspaces-across-tenants-using-azure-lighthouse"></a>使用 Azure Lighthouse 管理跨租使用者的工作區

如前所述，在許多情況下，不同的 Azure Sentinel 工作區可以位於不同的 Azure AD 租使用者中。 您可以使用 [Azure Lighthouse](../lighthouse/overview.md) 來跨租使用者界限延伸所有跨工作區活動，讓您管理租使用者中的使用者可以在所有租使用者的 Azure Sentinel 工作區上運作。 Azure Lighthouse [上線](../lighthouse/how-to/onboard-customer.md)之後，請使用 Azure 入口網站上的 [ [目錄 + 訂用帳戶選取器](./multiple-tenants-service-providers.md#how-to-access-azure-sentinel-from-other-tenants) ] 選取包含您想要管理之工作區的所有訂用帳戶，以確保所有訂用帳戶都能在入口網站中的不同工作區選取器中使用。

使用 Azure Lighthouse 時，建議您為每個 Azure Sentinel 角色建立群組，並將每個租使用者的許可權委派給這些群組。

## <a name="next-steps"></a>下一步
在本檔中，您已瞭解如何在多個工作區和租使用者之間擴充 Azure Sentinel 的功能。 如需有關實施 Azure Sentinel 的跨工作區架構的實用指導方針，請參閱下列文章：

- 瞭解如何使用 Azure Lighthouse 在 Azure Sentinel 中使用 [多個](./multiple-tenants-service-providers.md) 租使用者。
- 瞭解如何 [在多個工作區中順暢地查看和管理事件](./multiple-workspace-view.md) 。