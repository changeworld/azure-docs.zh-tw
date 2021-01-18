---
title: 開始使用更新的 Azure 計費帳戶
description: 開始使用更新的 Azure 計費帳戶，以了解新的計費和成本管理體驗有哪些變更
author: bandersmsft
ms.reviewer: amberbhargava
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 01/11/2021
ms.author: banders
ms.openlocfilehash: f0645115246995c9605563626d99bbf6a76784e1
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/12/2021
ms.locfileid: "98133547"
---
# <a name="get-started-with-your-updated-azure-billing-account"></a>開始使用更新的 Azure 計費帳戶

管理成本和發票是雲端體驗的重要元件之一。 可協助您控制及了解您在雲端上的支出。 為了讓您更輕鬆地管理成本和發票，Microsoft 正在更新您的 Azure 計費帳戶，使其包含增強的成本管理和計費功能。 本文將說明計費帳戶的更新，並引導您使用新功能。

> [!IMPORTANT]
> 當您收到來自 Microsoft 的電子郵件，通知您有關帳戶的更新時，您的帳戶即可進行更新。 我們會在帳戶更新前 60 天傳送通知。

## <a name="more-flexibility-with-your-new-billing-account"></a>使用新的計費帳戶更具彈性

下圖會比較您的新舊計費帳戶：

![此圖顯示新舊帳戶中計費階層之間的比較](./media/mosp-new-customer-experience/comparison-old-new-account.png)

新的計費帳戶包含一個或多個帳單設定檔，可讓您管理發票和付款方式。 每個帳單設定檔都包含一或多個發票區段，可讓您彙整帳單設定檔發票上的成本。

![顯示新計費階層的圖表](./media/mosp-new-customer-experience/new-billing-account-hierarchy.png)

計費帳戶上具有最高權限層級的角色。 這些角色應該指派給需要檢視發票，以及追蹤整個帳戶成本的使用者，例如組織中的財務或 IT 經理，或是註冊帳戶的個人。 如需詳細資訊，請參閱[計費帳戶角色和工作](../manage/understand-mca-roles.md#billing-account-roles-and-tasks)。 當您的帳戶更新時，舊計費帳戶中具有帳戶管理員角色的使用者會獲得新帳戶的擁有者角色。

## <a name="billing-profiles"></a>帳單設定檔

帳單設定檔可用來管理發票和付款方式。 您帳戶中的每個帳單設定檔都會在月初產生每月發票。 所有與帳單設定檔相關聯的訂用帳戶，其上個月的個別費用都會包含在發票中。

當您的帳戶更新時，系統會自動為每個訂用帳戶建立帳單設定檔。 訂用帳戶的費用會依其各自的帳單設定檔計費，並顯示在其發票上。

帳單設定檔上的角色有權檢視和管理發票和付款方式。 這些角色應指派給支付發票費用的使用者，例如組織中會計小組成員。 如需詳細資訊，請參閱[帳單設定檔角色和工作](../manage/understand-mca-roles.md#billing-profile-roles-and-tasks)。

當您的帳戶更新時，針對您授與其他人[檢視發票](download-azure-invoice.md#allow-others-to-download-the-your-subscription-invoice)權限的每個訂用帳戶，具有擁有者、參與者、讀者或帳單讀者 Azure 角色的使用者都會獲得各自帳單設定檔的「讀者」角色。

## <a name="invoice-sections"></a>發票區段

發票區段可用來彙整發票上的成本。 例如，您需要單一發票，但也想要依部門、小組或專案來彙整成本。 在此情況下，您可以設定單一帳單設定檔，而為每個部門、小組或專案分別建立一個發票區段。

當您的帳戶更新時，系統會為每個帳單設定檔建立發票區段，並將相關的訂用帳戶指派給發票區段。 當您新增更多訂用帳戶時，您可以建立更多區段並將訂用帳戶指派給發票區段。 在帳單設定檔的發票區段上，您會看到其針對每個指派至其中的訂用帳戶反映使用情況。

發票區段上的角色有權控制哪些人可建立 Azure 訂用帳戶。 這些角色應指派給負責為組織中的小組設定 Azure 環境的使用者，例如工程部門負責人和技術架構設計人員。 如需詳細資訊，請參閱[發票區段角色和工作](../manage/understand-mca-roles.md#invoice-section-roles-and-tasks)。

## <a name="enhanced-features-in-your-new-experience"></a>新體驗中的增強功能

您的新體驗包含下列成本管理和計費功能，您可以使用這些功能輕鬆地管理成本和發票：

#### <a name="invoice-management"></a>發票管理

**更多可預測的每月計費週期** - 在您的新帳戶中，計費週期會從當月的第一天開始，並在當月的最後一天結束，不論您何時註冊使用 Azure 都是如此。 發票將會在每個月的一開始產生，而且會包含上個月的所有費用。

**取得多個訂用帳戶的單一每月發票** - 您可以彈性地選擇每個訂用帳戶各使用一份每月發票，或針對多個訂用帳戶使用單一發票。

**收到 Azure 訂用帳戶、支援方案和 Azure Marketplace 產品的單一每月發票** - 您將會收到所有費用的單一每月發票，包括 Azure 訂用帳戶的使用量費用，以及支援方案和 Azure Marketplace 的購買費用。

**根據您的需求將發票上的成本分組** - 您可以根據您的需求，依據部門、專案或小組來將發票上的成本分組。

**在發票上設定選擇性的採購單號碼** - 若要將您的發票與內部財務系統建立關聯，請設定採購單號碼。 在 Azure 入口網站中隨時管理和更新發票。

#### <a name="payment-management"></a>付款管理

**使用信用卡立即支付發票** - 不需要等候自動付費系統向您的信用卡收取費用。 在 Azure 入口網站中，您可以使用任何信用卡來支付到期或逾期發票的費用。

**追蹤所有套用至帳戶的付款內容** - 在 Azure 入口網站中檢視所有套用至您帳戶的付款內容，包括所使用的付款方式、支付的金額，以及付款日期。

#### <a name="cost-management"></a>成本管理

**排定每月將使用量資料匯出至儲存體帳戶** - 以每日、每週或每月的頻率，自動將您的成本及使用量資料發佈到儲存體帳戶。

#### <a name="account-and-subscription-management"></a>帳戶及訂用帳戶管理

**指派多個管理員來執行計費作業** - 將帳單權限指派給多個使用者，以管理您帳戶的帳單。 有更多彈性可提供讀取、寫入或兩者的權限給其他人。

**直接在 Azure 入口網站中建立更多訂用帳戶** - 在 Azure 入口網站中單選即可建立所有訂用帳戶。

#### <a name="api-support"></a>API 支援

**透過 API、SDK 和 PowerShell 進行計費和成本管理作業** - 使用成本管理、計費和使用量 API，將帳單和成本資料提取到您慣用的資料分析工具中。

**透過 API、SDK 和 PowerShell 進行所有的訂用帳戶作業** - 使用 Azure 訂用帳戶 API 來自動化 Azure 訂用帳戶的管理，包括建立、重新命名和取消訂用帳戶。

## <a name="get-prepared-for-your-new-experience"></a>為您的新體驗做好準備

我們建議您採用下列方式，來為您的新體驗做好準備：

**每月計費週期和不同發票日期**

在新的體驗中，您的發票會在每個月的第九天產生，其中會包含上個月的所有費用。 此日期可能與您舊帳戶中產生發票的日期不同。 如果您與其他人共用您的發票，請通知他們該日期的變更。

**新的計費和成本管理 API**

如果您使用成本管理或計費 API 來查詢及更新帳單或成本資料，則務必使用新的 API。 下表列出無法搭配新計費帳戶使用的 API，以及您需要在新計費帳戶中進行的變更。

|API | 變更  |
|---------|---------|
|[計費帳戶 - 清單](/rest/api/billing/2019-10-01-preview/billingaccounts/list) | 在「計費帳戶 - 清單」API 中，您舊計費帳戶的 agreementType 為 **MicrosoftOnlineServiceProgram**，而新計費帳戶的 agreementType 為 **MicrosoftCustomerAgreement**。 如果您與 agreementType 具有相依性，請進行更新。 |
|[發票 - 依計費訂用帳戶列出](/rest/api/billing/2019-10-01-preview/invoices/listbybillingsubscription)     | 此 API 只會傳回您帳戶更新前所產生的發票。 您必須使用[發票 - 依計費帳戶列出](/rest/api/billing/2019-10-01-preview/invoices/listbybillingaccount) API 來取得新計費帳戶中產生的發票。 |

## <a name="cost-management-updates-after-account-update"></a>成本管理會在帳戶更新後更新

您的 Microsoft 客戶合約更新後的 Azure 帳單帳戶，可讓您在 Azure 入口網站中存取未曾透過隨用隨付帳戶取得的全新和擴充成本管理體驗。

### <a name="new-capabilities"></a>新功能

您的 Azure 計費帳戶可取得下列新功能。

#### <a name="new-billing-scopes"></a>新的計費範圍

在您的更新帳戶中，擁有新的成本管理 + 計費範圍。 除了協助階層式組織和發票開立之外，也可用來檢視來自多個基礎訂用帳戶的合併費用。 如需計費範圍的詳細資訊，請參閱 [Microsoft 客戶合約範圍](../costs/understand-work-scopes.md#microsoft-customer-agreement-scopes)。

您也可以存取成本管理 API，以取得較高範圍的合併成本檢視。 所有使用訂用帳戶範圍的成本管理 API 仍可在結構描述中進行一些次要變更。 如需 API 的詳細資訊，請參閱 [Azure 成本管理 API](/rest/api/cost-management/) 和 [Azure 使用量 API](/rest/api/consumption/)。

#### <a name="cost-allocation"></a>成本配置

透過您的更新帳戶，您可使用成本配置功能，從組織中的共用服務分配成本。 如需配置成本的詳細資訊，請參閱[建立和管理 Azure 成本配置規則](../costs/allocate-costs.md)。

#### <a name="power-bi"></a>Power BI

適用於 Power BI Desktop 的 Azure 成本管理連接器可協助您建立自訂視覺效果和 Azure 使用量和費用報告。 連線到您的更新帳戶之後，您可存取成本和使用量資料。 如需適用於 Power BI Desktop 的 Azure 成本管理連接器詳細資訊，請參閱[在 Power BI Desktop 中使用 Azure 成本管理連接器建立視覺效果和報告](/power-bi/connect-data/desktop-connect-azure-cost-management)。

### <a name="updated-capabilities"></a>更新的功能

您的 Azure 計費帳戶可取得下列更新功能。

#### <a name="cost-analysis"></a>成本分析

您可繼續檢視和追蹤每月耗用量成本，且您現在可以在成本分析中檢視保留和 Marketplace 購買成本。

使用您的更新帳戶，您會收到所有 Azure 費用的單一發票。 您現在也有簡化的單一每月日曆檢視，以取代您先前的計費期間檢視。

例如，如果舊帳戶的計費期間為 11 月 24 日至 12 月 23 日，則在升級之後，期間會變成 11 月 1 日到 11 月 30 日、12 月 1 日到 12 月 31 日，依此類推。

:::image type="content" source="./media/mosp-new-customer-experience/billing-periods.png" alt-text="顯示新舊計費週期之間比較的影像 " lightbox="./media/mosp-new-customer-experience/billing-periods.png" :::

#### <a name="budgets"></a>預算

您現在可為計費帳戶建立預算，讓您追蹤整個訂用帳戶的成本。 您也可以使用預算來即時掌握購買費用。 如需預算的詳細資訊，請參閱[建立和管理 Azure 預算](../costs/tutorial-acm-create-budgets.md)。

#### <a name="exports"></a>匯出

您的新計費帳戶會提供改良的匯出功能。 例如，您可以針對包含購買或分攤成本的實際成本建立匯出 (保留購買成本會分攤於整個購買期限)。 也可以建立計費帳戶的匯出，以取得計費帳戶中所有訂用帳戶的使用量和費用資料。 如需匯出的詳細資訊，請參閱[建立和管理匯出的資料](../costs/tutorial-export-acm-data.md)。

> [!NOTE]
> 在您的帳戶更新之前建立的匯出，[上個月成本的每月匯出] 類型將會匯出最後一個日曆月份的資料，而不是最後一個計費週期。

例如，對於從 12 月 23日到 1 月 22 日的計費期間，匯出的 CSV 檔案會有該期間的成本和使用量資料。 更新之後，匯出會包含日曆月份的資料。 例如，1 月 1 日到 1 月 31 日等等。

:::image type="content" source="./media/mosp-new-customer-experience/export-amortized-costs.png" alt-text="顯示新舊匯出詳細資料之間比較的影像 " lightbox="./media/mosp-new-customer-experience/export-amortized-costs.png" :::

## <a name="additional-information"></a>其他資訊

以下幾節將提供有關新體驗的其他資訊。

**服務不會中斷** 您訂用帳戶中的 Azure 服務可以持續執行，不會發生任何中斷。 唯一的更新是您的帳單體驗。 現有的資源、資源群組或管理群組不受影響。

**Azure 資源不會變更** 使用 Azure 角色型存取控制 (Azure RBAC) 設定的 Azure 資源存取權不會受到更新的影響。

**可在新體驗中取得過去的發票** 帳戶更新前所產生的發票，仍可在 Azure 入口網站中取得。

**在當月中更新帳戶的發票** 如果您的帳戶在月中間更新，您就會收到您帳戶更新之前所累積費用的單一發票。 您還會收到針對當月其餘時間計費的另一張發票。 例如，您的帳戶有一個訂用帳戶，並於 9 月 15 日進行更新。 您會收到一張 9 月 15 日前所累積費用的發票。 還會收到另一張週期為 9 月 15 日到 9 月 30 日的發票。 9 月之後，您將會每月會收到一張發票。

## <a name="need-help-contact-support"></a>需要協助嗎？ 請連絡支援人員。

如果需要協助，請[連絡支援人員](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解決您的問題。

## <a name="next-steps"></a>後續步驟

若要深入了解您的計費帳戶，請參閱下列文章。

- [了解新計費帳戶的管理角色](../manage/understand-mca-roles.md)
- [為您的新計費帳戶建立額外的 Azure 訂用帳戶](../manage/create-subscription.md)
- [在您的發票上建立區段以彙整成本](../manage/mca-section-invoice.md)