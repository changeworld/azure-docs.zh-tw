---
title: 適用於合作夥伴的 Azure 成本管理入門
description: 本文說明合作夥伴如何使用 Azure 成本管理功能，以及如何為其客戶啟用成本管理存取權。
author: bandersmsft
ms.author: banders
ms.date: 11/16/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: aparnag
ms.custom: secdec18
ms.openlocfilehash: cd3ca4d3ddf73469cd1f1fc065eccb369cf765af
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/09/2020
ms.locfileid: "96905667"
---
# <a name="get-started-with-azure-cost-management-for-partners"></a>適用於合作夥伴的 Azure 成本管理入門

Azure 成本管理依其原生狀態即適用於已將客戶登錄至 Microsoft 客戶合約，並且[已購買 Azure 方案](/partner-center/purchase-azure-plan)的直接合作夥伴。 本文說明合作夥伴如何使用 [Azure 成本管理](../index.yml)功能檢視 Azure 方案中的訂用帳戶成本。 此外也會說明合作夥伴如何為其客戶啟用零售費率上的成本管理存取。

針對直接合作夥伴和間接提供者，全域管理員和系統管理員代理人可以存取合作夥伴租用戶中的成本管理，以及以發票價格管理成本。

轉銷商和客戶則可存取客戶租用戶中的成本管理，並檢視訂用帳戶的成本，其中成本會以零售費率來計算和顯示。 不過，其必須具有客戶租用戶中訂用帳戶的 Azure RBAC 存取權，才能檢視成本。 客戶租用戶的提供者必須啟用成本可見性原則。

客戶可在其 CSP 合作夥伴啟用成本管理功能後使用這些功能。

CSP 合作夥伴可使用成本管理執行下列動作：

- 了解發票成本，並將成本與客戶、訂用帳戶、資源群組和服務產生關聯。
- 透過依據客戶、訂用帳戶、資源群組、資源、計量、服務和其他許多維度來分析成本的功能，在[成本分析](quick-acm-cost-analysis.md)中取得 Azure 成本的直覺觀點。
- 檢視成本分析中已套用合作夥伴所獲得的點數 (PEC) 的資源成本。
- 設定使用程式化[預算](tutorial-acm-create-budgets.md)的通知和自動化功能，以及在成本超出預算時發出的警示。
- 啟用讓客戶能夠存取成本管理資料的 Azure Resource Manager 原則。 其後，客戶將可針對使用[隨用隨付費率](https://azure.microsoft.com/pricing/calculator/)的訂用帳戶，檢視其使用量成本資料。
- 使用隨用隨付訂用帳戶將其成本和使用情況資料匯出至儲存體 Blob。

以下範例顯示所有客戶的成本。

[![此範例顯示所有客戶的成本](./media/get-started-partners/customer-costs1.png)](./media/get-started-partners/customer-costs1.png#lightbox)

以下範例顯示單一客戶的成本。

[![此範例顯示單一客戶的成本](./media/get-started-partners/customer-costs2.png)](./media/get-started-partners/customer-costs2.png#lightbox)

Azure 成本管理中提供的所有功能，也都適用於 REST API。 使用這些 API 可將成本管理工作自動化。

## <a name="prerequisites"></a>必要條件

就合作夥伴而言，Azure 成本管理依其原生狀態僅適用於 Azure 方案中的訂用帳戶。

若要在 Azure 入口網站中啟用 Azure 成本管理，您必須確認客戶已接受 Microsoft 客戶合約 (代表客戶)，並將客戶轉換至 Azure 方案。 只有已轉換至 Azure 方案的訂用帳戶成本，才可從 Azure 成本管理中存取。

Azure 成本管理需要對您的計費帳戶或訂用帳戶的讀取權限。

如需為計費帳戶啟用 Azure 成本管理及指派其存取權的詳細資訊，請參閱[指派使用者角色和權限](/partner-center/permissions-overview)。 「全域系統管理員」和「管理員代理人」角色可以管理計費帳戶的成本。

若要在訂用帳戶範圍存取 Azure 成本管理，任何對訂用帳戶具有 Azure RBAC 存取權的使用者，都可檢視以零售 (隨用隨付) 費率計算的成本。 不過，必須啟用[客戶租用戶的成本可見性原則](#enable-the-policy-to-view-azure-usage-charges)。 若要檢視支援帳戶類型的完整清單，請參閱[了解成本管理資料](understand-cost-mgt-data.md)。

## <a name="how-cost-management-uses-scopes"></a>成本管理使用範圍的方式

在範圍中，您可管理計費資料、指定付款專屬角色、檢視發票，以及進行一般帳戶管理。 計費和帳戶角色會與用於資源管理的範圍 (後者使用 Azure RBAC) 分開管理。 為了清楚區分不同範圍的意圖 (包括存取控制差異)，這些範圍分別稱為「計費範圍」和「Azure RBAC 範圍」。

若要了解計費範圍和 Azure RBAC 範圍，以及成本管理與範圍搭配運作的方式，請參閱[了解並使用範圍](understand-work-scopes.md)。

## <a name="manage-costs-with-partner-tenant-billing-scopes"></a>使用合作夥伴租用戶計費範圍來管理成本

在您將客戶登錄至 Microsoft 客戶合約之後，您的租用戶將有下列可用的 _計費範圍_。 使用範圍可在「成本管理」中管理成本。

### <a name="billing-account-scope"></a>計費帳戶範圍

使用計費帳戶範圍可檢視您所有客戶和帳單設定檔的稅前成本。 對於客戶在 Microsoft 客戶合約上以使用量為基礎的產品，才會顯示發票成本。 不過，對於客戶依據 Microsoft 客戶合約和 CSP 供應項目購買的產品，則會顯示發票成本。 目前，在該範圍中檢視成本的預設貨幣是美元。 針對該範圍而設定的預算也是以美元為單位。

無論客戶的帳單貨幣為何，合作夥伴在使用計費帳戶範圍對其客戶、訂用帳戶、資源和資源群組設定預算及管理成本時，都會以美元為單位。

合作夥伴在成本分析檢視中也會以特定的帳單貨幣來篩選客戶的成本。 選取 [實際成本] 清單，可用支援的客戶帳單貨幣檢視成本。

![此範例顯示為實際成本選取貨幣的情形](./media/get-started-partners/actual-cost-selector.png)

使用計費範圍中的[攤提成本檢視](quick-acm-cost-analysis.md#customize-cost-views)，可檢視保留執行個體在保留期間內的攤提成本。

### <a name="billing-profile-scope"></a>帳單設定檔範圍

使用帳單設定檔範圍，可用帳單貨幣檢視您所有的客戶在發票中包含的所有產品和訂用帳戶的稅前成本。 您可以使用 **InvoiceID** 篩選，針對特定發票篩選帳單設定檔中的成本。 此篩選會顯示特定發票的使用量和產品購買成本。 您也可以篩選發票上特定客戶的成本，以查看稅前成本。

在您將客戶登錄至 Microsoft 客戶合約後，您會收到發票，其中包含這些客戶在 Microsoft 客戶合約上的所有產品 (使用量、購買和權利) 的所有費用。 以相同的貨幣計費時，這些發票也會包含仍在 CSP 供應項目下的客戶在權利和購買的產品 (例如 SaaS、Azure Marketplace 和保留) 方面的費用。

為了方便對客戶發票進行費用的對帳，帳單設定檔範圍可讓您查看客戶的發票所產生的所有成本。 和發票一樣，該範圍會顯示每個客戶在新的 Microsoft 客戶合約中的成本。 該範圍也會顯示仍在現行 CSP 供應項目下的客戶權利產品所有的費用。

只有帳單設定檔和計費帳戶範圍這兩種範圍，才會顯示權利和購買產品 (例如 Azure Marketplace 和保留購買) 的費用。

帳單設定檔會定義發票中包含的訂用帳戶。 帳單設定檔在功能上與 Enterprise 合約註冊相同。 帳單設定檔是發票產生所在的範圍。

目前，客戶的帳單貨幣即為在帳單設定檔範圍中檢視成本時的預設貨幣。 對帳單設定檔範圍設定的預算，會以帳單貨幣為單位。

合作夥伴可使用該範圍對發票進行對帳。 而且，他們可使用該範圍以帳單貨幣設定下列項目的預算：

- 已篩選的特定發票
- 客戶
- 訂用帳戶
- 資源群組
- 資源
- Azure 服務
- 計量
- ResellerMPNID

### <a name="customer-scope"></a>客戶範圍

合作夥伴可使用此範圍來管理與登錄至 Microsoft 客戶合約的客戶相關聯的成本。 此範圍可讓合作夥伴以帳單貨幣檢視特定客戶的稅前成本。 您也可以篩選特定訂用帳戶、資源群組或資源的稅前成本。

客戶範圍不包含在現行 CSP 供應項目下的客戶。 此範圍僅包含具有 Microsoft 客戶合約的客戶。 當您套用客戶篩選時，可在計費帳戶和帳單設定檔範圍中存取現行 CSP 供應項目客戶的權利成本 (而非 Azure 使用情況)。 對此範圍設定的預算會以帳單貨幣為單位。

## <a name="partner-access-to-billing-scopes-in-cost-management"></a>合作夥伴在成本管理中對計費範圍的存取權

只有具備「全域系統管理員」和「管理員代理人」角色的使用者，才能直接在合作夥伴的 Azure 租用戶中管理及檢視計費帳戶、帳單設定檔和客戶的成本。 如需合作夥伴中心角色的詳細資訊，請參閱[指派使用者角色和權限](/partner-center/permissions-overview)。

## <a name="enable-cost-management-for-customer-tenant-subscriptions"></a>啟用客戶租用戶訂用帳戶的成本管理

合作夥伴可在客戶登錄至 Microsoft 客戶合約後啟用對成本管理的存取。 其後，合作夥伴將可啟用適當的原則，讓客戶能夠檢視其以隨用隨付零售費率計算的 Azure 取用服務成本。 在 Azure RBAC 訂用帳戶和資源群組範圍中，客戶的使用量成本會以其帳單貨幣顯示。

當合作夥伴啟用了成本可見性的原則時，任何對訂用帳戶具有 Azure Resource Manager 存取權的使用者，都可以管理和分析以隨用隨付費率計算的成本。 實際上，對 Azure 訂用帳戶具有適當 Azure RBAC 存取權的轉銷商和客戶，都可以檢視成本。

不論原則為何，只要提供者的全域管理員和管理員代理人有權存取訂用帳戶和資源群組，他們就可以檢視訂用帳戶成本。

### <a name="enable-the-policy-to-view-azure-usage-charges"></a>啟用檢視 Azure 使用費的原則

您必須是 **admin agent** 群組的成員，才能檢視和更新原則。 請使用下列資訊，啟用允許客戶檢視 Azure 使用費的原則。

在 Azure 入口網站中登入「合作夥伴租用戶」，然後選取 [成本管理 + 計費]。 在 [計費範圍] 區域中選取相關的計費範圍，然後選取 [客戶]。 客戶的清單會與計費帳戶產生關聯。 如果您不正確地登入客戶租用戶，您將不會看到 **客戶** 清單。

在客戶清單中，選取您要允許他們檢視成本的客戶。

[![在成本管理中選取客戶](./media/get-started-partners/customer-list.png)](./media/get-started-partners/customer-list.png#lightbox)

在 [設定] 底下，選取 [原則]。

針對與所選客戶的訂用帳戶相關聯的 [Azure 使用情況] 費用，會顯示目前的成本可見性原則。
![允許客戶檢視隨用隨付費用的原則](./media/get-started-partners/cost-management-billing-policies.png)

當此原則設定為 [否] 時，Azure 成本管理將不適用於與客戶相關聯的訂用帳戶使用者。 依預設會停用所有訂用帳戶使用者的成本可見性原則，除非合作夥伴加以啟用。

當成本原則設定為 [是] 時，與客戶租用戶相關聯的訂用帳戶使用者將可查看以隨用隨付費率計算的使用費。

成本可見性原則啟用時，所有具有訂用帳戶使用量的服務，都會顯示以隨用隨付費率計算的成本。 保留使用量的實際和攤提成本會將費用顯示為零。 購買和權利不會與特定訂用帳戶相關聯。 因此，購買不會顯示在訂用帳戶範圍中。 直接合作夥伴或間接提供者的全域管理員/管理代理人也可以使用[更新客戶 API](/rest/api/billing/2019-10-01-preview/policies/updatecustomer) 大規模設定每個客戶的成本可見性原則。

### <a name="view-subscription-costs-in-the-customer-tenant"></a>檢視客戶租用戶中的訂用帳戶成本

若要檢視訂用帳戶的成本，請在客戶的 Azure 租用戶中開啟 [成本管理 + 帳單]。 選取所需訂用帳戶的 [成本分析] 來開始檢閱成本。 您可以在客戶租用戶中個別檢視每個訂用帳戶的用量成本。

[![以客戶的身分檢視成本分析](./media/get-started-partners/subscription-costs.png)](./media/get-started-partners/subscription-costs.png#lightbox)

成本分析、預算和警示可按隨用隨付費率的成本，在訂用帳戶和資源群組 Azure RBAC 範圍中使用。

在 Azure RBAC 範圍中，保留執行個體的攤提檢視和實際成本會將費用顯示為零。 權利的購買成本 (例如保留執行個體和 Marketplace 費用) 只會顯示在合作夥伴租用戶 (購買行為所在位置) 的計費範圍中。

對於所有客戶來說，用於計算檢視中所顯示成本的零售費率，會與 Azure 定價計算機中所顯示的價格相同。 所顯示的成本不包括合作夥伴可能會有的任何折扣或點數，例如合作夥伴獲得的點數、階層折扣和全球服務折扣。

 

## <a name="analyze-costs-in-cost-analysis"></a>在成本分析中分析成本

對合作夥伴租用戶中的計費範圍具有存取權的合作夥伴可在不同客戶的成本分析中，探索及分析特定客戶或發票的發票成本。 在[成本分析](quick-acm-cost-analysis.md)檢視中，您也可以[儲存檢視](quick-acm-cost-analysis.md#saving-and-sharing-customized-views)，並將資料匯出至 [CSV 和 PNG 檔案](quick-acm-cost-analysis.md#download-usage-data)。

對客戶租用戶中的訂用帳戶具有存取權的 Azure RBAC 使用者也可以為客戶租用戶中的訂用帳戶分析零售成本、儲存檢視，以及將資料匯出至 CSV 和 PNG 檔案。

您可在成本分析中使用篩選和群組依據功能，依多個欄位來分析成本。 下一節會說明合作夥伴的特定欄位。

## <a name="data-fields"></a>資料欄位

下列資料欄位可在使用情況詳細資料檔案和成本管理 API 中找到。 在適用時，會顯示合作夥伴中心的對等資訊。 對於下列以粗體顯示的欄位，合作夥伴可在成本分析中使用篩選和群組依據功能，依多個欄位來分析成本。 粗體欄位僅適用於合作夥伴支援的 Microsoft 客戶合約。

| **欄位名稱** | **說明** | **合作夥伴中心的對等項目** |
| --- | --- | --- |
| invoiceId | 針對特定交易顯示在發票上的發票識別碼。 | 顯示交易的發票號碼。 |
| previousInvoiceID | 原始發票有退款 (負成本) 時的參考。 只有在有退款時才會填入。 | N/A |
| billingAccountName | 代表合作夥伴的計費帳戶名稱。 此欄位會累計已登錄至 Microsoft 客戶合約的客戶和已進行權利購買 (如 SaaS、Azure Marketplace 和保留) 的 CSP 客戶所產生的所有成本。 | N/A |
| billingAccountID | 代表合作夥伴的計費帳戶識別碼。 | MCAPI 合作夥伴商業根識別碼。 用於要求中，但不包含在回應內。|
| billingProfileID | 此為帳單設定檔的識別碼；該設定檔會針對已登錄至 Microsoft 客戶合約的客戶和已進行權利購買 (如 SaaS、Azure Marketplace 和保留) 的 CSP 客戶，以單一帳單貨幣為發票的成本進行分組。 | MCAPI 合作夥伴計費群組識別碼。 用於要求中，但不包含在回應內。 |
| billingProfileName | 此為帳單設定檔的名稱；該設定檔會針對已登錄至 Microsoft 客戶合約的客戶和已進行權利購買 (如 SaaS、Azure Marketplace 和保留) 的 CSP 客戶，以單一帳單貨幣為發票的成本進行分組。 | N/A |
| invoiceSectionName | 在發票中計費之專案的名稱。 不適用於合作夥伴所登錄的 Microsoft 客戶合約。 | N/A |
| invoiceSectionID | 在發票中計費之專案的識別碼。 不適用於合作夥伴所登錄的 Microsoft 客戶合約。 | N/A |
| **CustomerTenantID** | 客戶訂用帳戶的 Azure Active Directory 租用戶的識別碼。 | 客戶的組織識別碼 - 客戶的 Azure Active Directory TenantID。 |
| **CustomerName** | 客戶訂用帳戶的 Azure Active Directory 租用戶的名稱。 | 客戶的組織名稱，如合作夥伴中心所示。 對於發票與系統資訊的對帳而言非常重要。 |
| **CustomerTenantDomainName** | 客戶訂用帳戶的 Azure Active Directory 租用戶的網域名稱。 | 客戶 Azure Active Directory 租用戶網域。 |
| **PartnerTenantID** | 合作夥伴 Azure Active Directory 租用戶的識別碼。 | 合作夥伴 Azure Active Directory 租用戶識別碼，名為「合作夥伴識別碼」，採用 GUID 格式。 |
| **PartnerName** | 合作夥伴 Azure Active Directory 租用戶的名稱。 | 合作夥伴名稱。 |
| **ResellerMPNID** | 選取與訂用帳戶相關聯之轉銷商的 MPNID。 | 訂用帳戶記錄所列轉銷商的 MPN 識別碼。 不適用於目前的活動。 |
| costCenter | 與訂用帳戶相關聯的成本中心。 | N/A |
| billingPeriodStartDate | 計費週期開始日期，如發票所示。 | N/A |
| billingPeriodEndDate | 計費週期結束日期，如發票所示。 | N/A |
| servicePeriodStartDate | 評等服務使用方式的費用時的評等期間開始日期。 Azure 服務的價格會依據評等期間來決定。 | 合作夥伴中心的 ChargeStartDate。  計費週期的開始日期，但在呈現先前的計費週期內未收費的潛在使用量資料的日期時除外。 此時間一律為第一天的開始時間，即 0:00。 |
| servicePeriodEndDate | 評等服務使用方式的費用時的期間結束日期。 Azure 服務的價格會依據評等期間來決定。 | N/A |
| date | 針對 Azure 使用量資料，會顯示評等使用量的日期。 針對保留執行個體，會顯示購買的日期。 針對週期性費用和一次性費用 (例如 Marketplace 和支援)，會顯示購買日期。 | N/A |
| productID | 因使用量或購買而產生費用之產品的識別碼。 這是 productID 和 SKuID 的串連索引鍵，如合作夥伴中心所示。 | 產品的識別碼。 |
| product | 因使用量或購買而產生費用之產品的名稱。 | 目錄中的產品名稱。 |
| serviceFamily | 顯示購買或收費之產品的服務系列。 例如，儲存體或計算。 | N/A |
| productOrderID | 訂用帳戶所屬的資產或 Azure 方案名稱的識別碼。 例如，Azure 方案。 | CommerceSubscriptionID |
| productOrderName | 訂用帳戶所屬的 Azure 方案名稱。 例如，Azure 方案。 | N/A|
| consumedService | 舊版 EA 使用情況詳細資料中使用的已取用服務 (舊版分類法)。 | 合作夥伴中心顯示的服務。 例如 Microsoft.Storage、Microsoft.Compute 和 microsoft.operationalinsights。 |
| meterID | 測量使用量的計量識別碼。 | 所用計量的識別碼。 |
| meterName | 識別測量使用量的計量名稱。 | 所用計量的名稱。 |
| meterCategory | 識別使用量的最上層服務。 | 使用量的最上層服務。 |
| meterSubCategory | 定義可能會影響費率的 Azure 服務類型或子類別。 | 可能會影響費率的 Azure 服務類型。|
| meterRegion | 針對根據資料中心位置定價的某些服務，識別資料中心的位置。 | 服務資料中心的區域位置 (如果適用並已填入)。 |
| 訂用帳戶識別碼 | Microsoft 為 Azure 訂用帳戶產生的唯一識別碼。 | EntitlementID |
| subscriptionName | Azure 訂用帳戶的名稱。 | N/A |
| 詞彙 | 顯示供應項目有效性的詞彙。 例如，保留執行個體會顯示保留執行個體每年 12 個月的期間。 一次性購買或週期性購買的期間會顯示為一個月 (針對 SaaS、Azure Marketplace 和支援)。 不適用於 Azure 使用量。 | N/A |
| publisherType (firstParty、thirdPartyReseller、thirdPartyAgency) | 將發行者識別為第一方、第三方轉銷商或第三方代理商的發行者類型。 | N/A |
| partNumber | 未使用的保留執行個體和 Azure Marketplace 服務的組件編號。 | N/A |
| publisherName | 服務發行者 (包括 Microsoft 或第三方發行者) 的名稱。 | 產品發行者的名稱。|
| reservationId | 保留執行個體購買的識別碼。 | N/A |
| reservationName | 保留執行個體的名稱。 | N/A |
| reservationOrderId | 保留執行個體的 OrderID。 | N/A |
| frequency | 保留執行個體的付款頻率。 | N/A |
| resourceGroup | 用於生命週期資源管理的 Azure 資源群組名稱。 | 資源群組的名稱。 |
| instanceID (或) ResourceID | 資源執行個體的識別碼。 | 顯示為包含完整資源屬性的 ResourceURI。 |
| resourceLocation | 資源位置的名稱。 | 資源的位置。 |
| Location | 資源的標準化位置。 | N/A |
| effectivePrice | 服務的有效單位價格 (以定價貨幣為單位)。 每個產品、服務系列、計量和供應項目各不相同。 與計費帳戶的價位表中的價格搭配使用。 有分層式價格或內含的數量時，會顯示使用量的混合價格。 | 進行調整後的單位價格。 |
| 數量 | 已購買或使用的測量數量。 計費期間所使用的計量數量。 | 單位數目。 請確定此數目符合您的帳單系統在對帳期間所包含的資訊。 |
| unitOfMeasure | 識別服務的計費單位。 例如，GB 和小時。 | 識別服務的計費單位。 例如，GB、小時和 10,000 秒。 |
| pricingCurrency | 定義單位價格的貨幣。 | 價格清單中的貨幣。|
| billingCurrency | 定義計費成本的貨幣。 | 客戶所在地理區域的貨幣。 |
| chargeType | 定義成本在 Azure 成本管理中顯示的費用類型，例如購買和退款。 | 費用或調整的類型。 不適用於目前的活動。 |
| costinBillingCurrency | 稅前的延伸成本或混合成本 (以計費貨幣為單位)。 | N/A |
| costinPricingCurrency | 要與價格相互關聯的稅前延伸成本或混合成本 (以帳單貨幣為單位)。 | N/A |
| **costinUSD** | 稅前的預估延伸成本或混合成本 (以美元為單位)。 | N/A |
| **paygCostInBillingCurrency** | 如果價格是零售價格，則會顯示成本。 以帳單貨幣顯示隨用隨付價格。 僅適用於 Azure RBAC 範圍。 | N/A |
| **paygCostInUSD** | 如果價格是零售價格，則會顯示成本。 以美元顯示隨用隨付價格。 僅適用於 Azure RBAC 範圍。 | N/A |
| exchangeRate | 用來從定價貨幣轉換成帳單貨幣的匯率。 | 在合作夥伴中心稱為 PCToBCExchangeRate。 從定價貨幣轉換成帳單貨幣的匯率。|
| exchangeRateDate | 用來從定價貨幣轉換成帳單貨幣的匯率日期。 | 在合作夥伴中心稱為 PCToBCExchangeRateDat。 從定價貨幣轉換成帳單貨幣的匯率日期。|
| isAzureCreditEligible | 指出成本是否符合以 Azure 點數付款的資格。 | N/A |
| serviceInfo1 | 舊版欄位，可擷取選擇性的服務特定中繼資料。 | 內部 Azure 服務中繼資料。 |
| serviceInfo2 | 舊版欄位，可擷取選擇性的服務特定中繼資料。 | 服務資訊。 例如，虛擬機器的映像類型和 ExpressRoute 的 ISP 名稱。|
| additionalInfo | 服務專屬的中繼資料。 例如，虛擬機器的影像類型。 | 任何未涵蓋於其他資料行中的其他資訊。 服務特有的中繼資料。 例如，虛擬機器的影像類型。|
| tags | 您指派給計量的標記。 使用標記為帳單記錄分組。 例如，您可以使用標記，根據使用計量的部門散發成本。 | 客戶新增的標記。|
| **partnerEarnedCreditRate** | 可透過合作夥伴管理員連結存取合作夥伴所獲得的點數 (PEC) 時適用的折扣率。 | 合作夥伴所獲得的點數 (PEC) 的折扣率。 例如 0% 或 15%。 |
| **partnerEarnedCreditApplied** | 指出是否已套用合作夥伴所獲得的點數。 | N/A |

## <a name="view-partner-earned-credit-pec-resource-costs"></a>檢視合作夥伴所獲得的點數 (PEC) 資源成本

在 Azure 成本管理中，合作夥伴可以使用成本分析來檢視獲得 PEC 權益的成本。

在 Azure 入口網站中登入合作夥伴租用戶，然後選取 [成本管理 + 計費]。 在 [成本管理] 底下，選取 [成本分析]。

[成本分析] 檢視會顯示合作夥伴計費帳戶的成本。 視需要針對合作夥伴、特定客戶或帳單設定檔選取 [範圍]，以進行發票的對帳。

在環圈圖中選取下拉式清單，然後選取 **PartnerEarnedCreditApplied** 以深入探索 PEC 成本。

![此範例顯示如何檢視合作夥伴獲得的點數](./media/get-started-partners/cost-analysis-pec1.png)

當 **PartnerEarnedCreditApplied** 屬性為 _True_ 時，相關聯的成本會具有讓合作夥伴獲得權益的管理員存取權。

當 **PartnerEarnedCreditApplied** 屬性為 _False_ 時，表示相關聯的成本不符合獲得點數的必要資格。 或者，購買的服務不符合讓合作夥伴取得點數的資格。

服務使用方式資料通常需要 8-24 小時才會出現在成本管理中。 如需詳細資訊，請參閱[成本和使用量資料更新與保留](understand-cost-mgt-data.md#cost-and-usage-data-updates-and-retention)。 PEC 點數會在存取後的 48 小時內出現在 Azure 成本管理中。


您也可以使用 [群組依據] 選項，依 **PartnerEarnedCreditApplied** 屬性進行分組和篩選。 請使用這些選項來查看具有和沒有 PEC 的成本。

![依合作夥伴獲得的點數進行分組或篩選](./media/get-started-partners/cost-analysis-pec2.png)

## <a name="export-cost-data-to-azure-storage"></a>將成本資料匯出至 Azure 儲存體

對合作夥伴租用戶中的計費範圍具有存取權的合作夥伴，可以將其成本和使用情況資料匯出至 Azure 儲存體 Blob。 Blob 必須位於合作夥伴租用戶中的訂用帳戶上，且該訂用帳戶不可以是[共用的服務訂用帳戶](/partner-center/shared-services)或客戶的訂用帳戶。 若要啟用成本資料匯出，建議您在合作夥伴租用戶中設定獨立的隨用隨付訂用帳戶，以裝載匯出的成本資料。 系統會在隨用隨付訂用帳戶所裝載的 Azure 儲存體 Blob 上建立匯出儲存體帳戶。 根據夥伴建立匯出的範圍，相關聯的資料會定期自動匯出至儲存體帳戶。

對訂用帳戶具有 Azure RBAC 存取權的使用者，也可以將成本資料匯出至客戶租用戶中的任何訂用帳戶所裝載的 Azure 儲存體 Blob。

### <a name="create-an-export-in-a-partner-tenant-or-customer-tenant"></a>在合作夥伴租用戶或客戶租用戶中建立匯出

在 Azure 入口網站中，登入合作夥伴租用戶或客戶租用戶，然後選取 [成本管理 + 計費]。 選取適當的範圍 (例如 Microsoft 合作夥伴合約計費帳戶)，然後選取 [成本分析]。 在頁面載入後，選取 [匯出]。 選取 [排程匯出] 底下的 [檢視所有匯出]。

![選取匯出並檢視所有匯出](./media/get-started-partners/export01.png)

接著，選取 [新增] 並輸入名稱，然後選取匯出類型。 選取 [儲存體] 索引標籤，然後輸入必要的資訊。

![新增匯出並選取儲存體索引標籤](./media/get-started-partners/export02.png)

在合作夥伴租用戶中建立匯出時，請選取合作夥伴租用戶中的隨用隨付訂用帳戶。 使用該訂用帳戶建立 Azure 儲存體帳戶。

針對客戶租用戶中的 Azure RBAC 使用者，選取客戶租用戶中的訂用帳戶。 使用該訂用帳戶建立 Azure 儲存體帳戶。

檢查內容，然後選取 [建立] 以排程匯出。

若要驗證匯出清單中的資料，請選取儲存體帳戶名稱。 在 [儲存體帳戶] 頁面上選取 [容器]，然後選取容器。 瀏覽至對應的資料夾，並選取 CSV 檔案。 選取 [下載] 以取得 CSV 檔案，並加以開啟。 匯出的資料會類似於 Azure 入口網站中的成本資料和使用情況詳細資料。

![匯出的資料範例](./media/get-started-partners/example-export-data.png)

## <a name="cost-management-rest-apis"></a>成本管理 REST API

合作夥伴和客戶可使用下列各節所說明的成本管理 API 來執行一般工作。

### <a name="azure-cost-management-apis---direct-and-indirect-providers"></a>Azure 成本管理 API - 直接和間接提供者

對合作夥伴租用戶中的計費範圍具有存取權的合作夥伴，可以使用下列 API 來檢視發票成本。

無論成本原則為何，訂用帳戶範圍中的 API 皆可由合作夥伴呼叫，只要他們具有訂用帳戶的存取權即可。 具有訂用帳戶存取權的其他使用者 (例如客戶或轉銷商) 只有在合作夥伴為客戶租用戶啟用成本原則之後，才能呼叫 API。


#### <a name="to-get-a-list-of-billing-accounts"></a>取得計費帳戶的清單

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?api-version=2019-10-01-preview
```

#### <a name="to-get-a-list-of-customers"></a>取得客戶的清單

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/customers?api-version=2019-10-01-preview
```

#### <a name="to-get-a-list-of-subscriptions"></a>取得訂用帳戶的清單

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/billingSubscriptions?api-version=2019-10-01-preview
```

#### <a name="to-get-a-list-of-invoices-for-a-period-of-time"></a>取得一段時間內的發票清單

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/invoices?api-version=2019-10-01-preview&periodStartDate={periodStartDate}&periodEndDate={periodEndDate}
```

API 呼叫會傳回一個發票陣列，其中包含與下列 JSON 程式碼類似的元素。

```
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/{billingAccountID}/billingProfiles/{BillingProfileID}/invoices/{InvoiceID}",
      "name": "{InvoiceID}",
      "properties": {
        "amountDue": {
          "currency": "USD",
          "value": x.xx
        },
        ...
    }
```

使用前述傳回的識別碼欄位值，並在下列範例中將其取代為用來查詢使用情況詳細資料的範圍。

```
GET https://management.azure.com/{id}/providers/Microsoft.Consumption/UsageDetails?api-version=2019-10-01
```

此範例會傳回與特定發票相關聯的使用量記錄。


#### <a name="to-get-the-policy-for-customers-to-view-costs"></a>取得讓客戶檢視成本的原則

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/customers/{customerID}/policies/default?api-version=2019-10-01-preview
```

#### <a name="to-set-the-policy-for-customers-to-view-costs"></a>設定讓客戶檢視成本的原則

```
PUT https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/customers/{customerID}/policies/default?api-version=2019-10-01-preview
```

#### <a name="to-get-azure-service-usage-for-a-billing-account"></a>取得計費帳戶的 Azure 服務使用方式

```
GET https://management.azure.com/providers/Microsoft.Billing/BillingAccounts/{billingAccountName}/providers/Microsoft.Consumption/usageDetails?api-version=2019-10-01
```

#### <a name="to-download-a-customers-azure-service-usage"></a>下載客戶的 Azure 服務使用方式

下列 get 呼叫是非同步作業。

```
GET https://management.azure.com/Microsoft.Billing/billingAccounts/{billingAccountName}/customers/{customerID}/providers/Microsoft.Consumption/usageDetails/download?api-version=2019-10-01 -verbose
```

呼叫回應中所傳回的 `Location` URI，以檢查作業狀態。 當狀態為「已完成」時，`downloadUrl` 屬性會包含一個連結，供您用來下載產生的報表。


#### <a name="to-get-or-download-the-price-sheet-for-consumed-azure-services"></a>取得或下載所用 Azure 服務的價位表

首先，使用下列貼文。

```
POST https://management.azure.com/providers/Microsoft.Billing/BillingAccounts/{billingAccountName}/billingProfiles/{billingProfileID}/pricesheet/default/download?api-version=2019-10-01-preview&format=csv" -verbose
```

然後，呼叫非同步作業的屬性值。 例如：

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/billingProfiles/{billingProfileID}/pricesheetDownloadOperations/{operation}?sessiontoken=0:11186&api-version=2019-10-01-preview
```
前述 get 呼叫會傳回包含價位表的下載連結。


#### <a name="to-get-aggregated-costs"></a>取得彙總成本

```
POST https://management.azure.com/providers/microsoft.billing/billingAccounts/{billingAccountName}/providers/microsoft.costmanagement/query?api-version=2019-10-01
```

#### <a name="create-a-budget-for-a-partner"></a>為合作夥伴建立預算

```
PUT https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/providers/Microsoft.CostManagement/budgets/partnerworkshopbudget?api-version=2019-10-01
```

#### <a name="create-a-budget-for-a-customer"></a>為客戶建立預算

```
PUT https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/customers/{customerID}/providers/Microsoft.Consumption/budgets/{budgetName}?api-version=2019-10-01
```

#### <a name="delete-a-budget"></a>刪除預算

```
DELETE
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/providers/Microsoft.CostManagement/budgets/{budgetName}?api-version=2019-10-01
```


## <a name="next-steps"></a>後續步驟
- 在成本管理中[開始分析成本](quick-acm-cost-analysis.md)
- 在成本管理中[建立和管理預算](tutorial-acm-create-budgets.md)
