---
title: 根據您的需求整理發票 - Azure
description: 了解如何以您的發票整理成本。
author: amberbhargava
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: banders
ms.openlocfilehash: c9cb1d7d1dcc6e7872b22d8c58fe44b9bce25f13
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "77200739"
---
# <a name="organize-costs-by-customizing-your-billing-account"></a>自訂計費帳戶以整理成本

您的 Microsoft 客戶合約計費帳戶可讓您彈性地根據自己的需求來整理成本，無論是依部門、專案或開發環境。

本文說明如何使用 Azure 入口網站來整理成本。 內容適用於 Microsoft 客戶合約的計費帳戶。 請[確認您是否有 Microsoft 客戶合約的存取權](#check-access-to-a-microsoft-customer-agreement)。

## <a name="structure-your-account-with-billing-profiles-and-invoice-sections"></a>為帳戶設定帳單設定檔和發票區段結構

在 Microsoft 客戶合約的計費帳戶中，使用帳單設定檔和發票區段來整理成本。

![顯示 Microsoft 客戶合約帳單階層的螢幕擷取畫面](./media/mca-section-invoice/mca-hierarchy.png)

### <a name="billing-profile"></a>帳單設定檔

帳單設定檔代表發票和相關的計費資訊，例如付款方式和帳單地址。 您帳戶中的每個帳單設定檔都會在月初產生每月發票。 發票包含 Azure 使用量的費用以及上個月其他購買所產生的費用。

在您註冊 Azure 時，系統會自動為您的計費帳戶建立帳單設定檔。 您可以建立額外的帳單設定檔，以數個月的發票整理成本。

> [!IMPORTANT]
>
> 建立額外的帳單設定檔可能會影響您的整體成本。 如需詳細資訊，請參閱[新增帳單設定檔時要考慮的事項](#things-to-consider-when-adding-new-billing-profiles)。

### <a name="invoice-section"></a>發票區段

發票區段代表發票中的一個成本群組。 系統會在您的帳戶中自動為每個帳單設定檔建立發票區段。 您可以根據您的需求，建立額外的區段來整理成本。 每個發票區段都會顯示在發票上，並附上該月份產生的費用。

下圖顯示的發票包含「工程」和「行銷」兩個發票區段。 每個區段的摘要和詳細費用都會顯示在發票中。 圖中顯示的價格僅供示範，不代表 Azure 服務的實際價格。

![顯示發票和區段的圖片](./media/mca-section-invoice/mca-invoice-with-sections.png)

## <a name="billing-account-structure-for-common-scenarios"></a>常見狀況的計費帳戶結構

本節說明整理成本和對應計費帳戶結構的常見狀況：

|狀況  |結構  |
|---------|---------|
|Jack 註冊 Azure，且需要每個月的發票。 | 一個帳單設定檔和一個發票區段。 Jack 註冊 Azure 時，系統會自動設定此結構，不需要任何額外的步驟。 |

![簡單計費狀況的資訊圖](./media/mca-section-invoice/organize-billing-scenario1.png)

|狀況  |結構  |
|---------|---------|
|Contoso 是一個小型組織，需要每個月的發票，並依據部門 (行銷和工程) 將成本分組。  | 一個 Contoso 的帳單設定檔，以及行銷和工程部門各一個發票區段。 |

![簡單計費狀況的資訊圖](./media/mca-section-invoice/organize-billing-scenario2.png)

|狀況  |結構  |
|---------|---------|
|Fabrikam 是中型組織，其工程和行銷部門需要分開的發票。 工程部門想要依環境 (生產和開發) 將成本分組。  | 行銷和工程部門各一個帳單設定檔。 工程部門的生產和開發環境各一個發票區段。 |

![簡單計費狀況的資訊圖](./media/mca-section-invoice/organize-billing-scenario3.png)

## <a name="create-a-new-invoice-section"></a>建立新的發票區段

若要建立發票區段，您必須是**帳單設定檔擁有者**或**帳單設定檔參與者**。 如需詳細資訊，請參閱[管理帳單設定檔的發票區段](understand-mca-roles.md#manage-invoice-sections-for-billing-profile)。

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 搜尋 [成本管理 + 帳單]  。

   ![顯示在入口網站中搜尋 [成本管理 + 帳單] 的螢幕擷取畫面](./media/mca-section-invoice/search-cmb.png)

3. 從左側窗格中選取 [帳單設定檔]  。 從清單中選取 [帳單設定檔]。 新的區段會顯示在所選帳單設定檔的發票上。

   [![顯示帳單設定檔清單的螢幕擷取畫面](./media/mca-section-invoice/mca-select-profile.png)](./media/mca-section-invoice/mca-select-profile-zoomed-in.png#lightbox)

4. 從左側窗格中選取 [發票區段]  ，然後從頁面頂端選取 [新增]  。

   [![顯示新增發票區段的螢幕擷取畫面](./media/mca-section-invoice/mca-list-invoice-sections.png)](./media/mca-section-invoice/mca-list-invoice-sections-zoomed-in.png#lightbox)

5. 輸入發票區段的名稱。

   [![顯示發票區段建立頁面的螢幕擷取畫面](./media/mca-section-invoice/mca-create-invoice-section.png)](./media/mca-section-invoice/mca-create-invoice-section-zoomed-in.png#lightbox)

6. 選取 [建立]  。

## <a name="create-a-new-billing-profile"></a>建立新的帳單設定檔

若要建立帳單設定檔，您必須是**計費帳戶擁有者**或**計費帳戶參與者**。 如需詳細資訊，請參閱[管理計費帳戶的帳單設定檔](understand-mca-roles.md#manage-billing-profiles-for-billing-account)。

> [!IMPORTANT]
>
> 建立額外的帳單設定檔可能會影響您的整體成本。 如需詳細資訊，請參閱[新增帳單設定檔時要考慮的事項](#things-to-consider-when-adding-new-billing-profiles)。

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 搜尋 [成本管理 + 帳單]  。

   ![顯示在入口網站中搜尋 [成本管理 + 帳單] 的螢幕擷取畫面](./media/mca-section-invoice/search-cmb.png)

3. 從左側窗格中選取 [帳單設定檔]  ，然後從頁面頂端選取 [新增]  。

   [![顯示帳單設定檔清單的螢幕擷取畫面](./media/mca-section-invoice/mca-list-profiles.png)](./media/mca-section-invoice/mca-list-profiles-zoomed-in.png#lightbox)

    > [!Note]
    >
    > 如果您在 [帳單設定檔] 頁面中沒看到 [新增] 按鈕，表示您的帳戶無法使用此功能。 目前此功能僅提供給經 Microsoft 業務代表接洽設定的帳戶。

4. 填妥表單，然後按一下 [建立]  。

   [![顯示帳單設定檔建立頁面的螢幕擷取畫面](./media/mca-section-invoice/mca-add-profile.png)](./media/mca-section-invoice/mca-add-profile-zoomed-in.png#lightbox)

    |欄位  |定義  |
    |---------|---------|
    |名稱     | 顯示名稱可協助您在 Azure 入口網站中輕鬆識別帳單設定檔。  |
    |PO 編號    | 選擇性的採購單號碼。 PO 編號會顯示在為帳單設定檔產生的發票上。 |
    |帳單地址   | 帳單地址會顯示在為帳單設定檔產生的發票上。 |
    |電子郵件發票   | 勾選電子郵件發票方塊，即可透過電子郵件接收此帳單設定檔的發票。 如未勾選，可以在 Azure 入口網站中檢視及下載發票。|

5. 選取 [建立]  。

## <a name="link-charges-to-invoice-sections-and-billing-profiles"></a>連結費用與發票區段和帳單設定檔

根據您的需求自訂計費帳戶之後，您可以將訂用帳戶和其他產品連結到您想要的發票區段和帳單設定檔。

### <a name="link-a-new-subscription"></a>連結新的訂用帳戶

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 尋找**訂用帳戶**。

   [![顯示在入口網站中搜尋訂用帳戶的螢幕擷取畫面](./media/mca-section-invoice/search-subscriptions.png)](./media/mca-section-invoice/search-subscriptions.png#lightbox)

3. 從頁面頂端選取 [新增]  。

   ![在 [訂用帳戶] 檢視中顯示 [新增] 按鈕的螢幕擷取畫面](./media/mca-section-invoice/subscription-add.png)

4. 如果您有多個計費帳戶的存取權，請選取 Microsoft 客戶合約的計費帳戶。

   ![在 [訂用帳戶] 檢視中顯示 [新增] 按鈕的螢幕擷取畫面](./media/mca-section-invoice/mca-create-azure-subscription.png)

5. 選取要對其收取訂用帳戶使用量費用的帳單設定檔。 此訂用帳戶的 Azure 使用量費用和其他購置將會計入選取帳單設定檔的發票。

6. 選取發票區段以連結訂用帳戶的費用。 費用會顯示在此區段下帳單設定檔的發票上。

7. 選取 Azure 方案，並為您的訂用帳戶輸入易記的名稱。

9. 按一下頁面底部的 [新增]  。  

### <a name="link-existing-subscriptions-and-products"></a>連結現有的訂用帳戶和產品

如果您有現有的 Azure 訂用帳戶或其他產品，例如 Azure Marketplace、應用程式來源資源，您可以將它們從其現有的發票區段移至另一個發票區段，以重新整理您的成本。

> [!IMPORTANT]
>
> 訂用帳戶和其他產品只能在屬於相同帳單設定檔的發票區段之間移動。 不支援跨不同帳單設定檔中的發票區段移動訂用帳戶和產品。

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 搜尋 [成本管理 + 帳單]  。

   ![顯示在入口網站中搜尋訂用帳戶的螢幕擷取畫面](./media/mca-section-invoice/search-cmb.png)

3. 若要將訂用帳戶連結至新的發票區段，請選取畫面左側的 [Azure 訂用帳戶]  。 針對其他產品 (例如 Azure Marketplace、應用程式來源資源)，請選取 [週期性費用]  。

   [![顯示變更發票區段選項的螢幕擷取畫面](./media/mca-section-invoice/mca-select-change-invoice-section.png)](./media/mca-section-invoice/mca-select-change-invoice-section.png#lightbox)

4. 在此頁面中，按一下您要連結到新發票區段的訂用帳戶或產品的省略號 (三個點)。 選取 [變更發票區段]  。

5. 從下拉式清單中選取新的發票區段。 下拉式清單中只會顯示與現有發票區段相同帳單設定檔相關聯的發票區段。

    [![顯示選取新發票區段的螢幕擷取畫面](./media/mca-section-invoice/mca-select-new-invoice-section.png)](./media/mca-section-invoice/mca-select-new-invoice-section-zoomed-in.png#lightbox)

6. 選取 [儲存]  。

## <a name="things-to-consider-when-adding-new-billing-profiles"></a>新增帳單設定檔時要考慮的事項

### <a name="azure-usage-charges-may-be-impacted"></a>可能會影響 Azure 使用量費用

在 Microsoft 客戶合約的計費帳戶中，每個帳單設定檔都會按月彙總 Azure 使用量。 具有分層定價的 Azure 資源價格是根據每個帳單設定檔的使用量分別決定。 計算價格時，不會跨帳單設定檔彙總使用量。 以具有多個帳單設定檔的帳戶來說，這可能會影響 Azure 使用量的整體成本。

讓我們看看這兩種案例的成本變化。 案例中使用的價格僅供示範，不代表 Azure 服務的實際價格。

#### <a name="you-only-have-one-billing-profile"></a>您只有一個帳單設定檔。

假設您使用 Azure 區塊 Blob 儲存體，其費用為前 50 TB 每一 GB .00184 美元，之後的 450 TB 每一 GB .00177 美元。 在您的帳單設定檔中計費的訂用帳戶使用了 100 TB，以下是您要支付的費用。

|  分層定價 (美元) |數量 | 金額 (美元)|
|---------|---------|---------|
|前 50 TB 的每一 TB 1.84 美元/月    | 50 TB        | 92.0   |
|之後 450 TB 的每一 TB 1.77 美元/月    |  50 TB         | 88.5   |
|總計     |     100 TB  | 180.5

在此案例中，使用 100 TB 資料的費用總計是 **180.5**。

#### <a name="you-have-multiple-billing-profiles"></a>您有多個帳單設定檔。

現在，假設您又建立了另一個帳單設定檔，然後透過第一個帳單設定檔中的計費訂用帳戶使用了 50 GB，也透過第二個帳單設定檔中的計費訂用帳戶使用了 50 GB，以下是您要支付的費用。

`Charges for the first billing profile`

|  分層定價 (美元) |數量 | 金額 (美元)|
|---------|---------|---------|
|前 50 TB 的每一 TB 1.84 美元/月    | 50 TB        | 92.0  |
|之後 450 TB 的每一 TB 1.77 美元/月    |  0 TB         | 0.0  |
|總計     |     50 TB  | 92.0

`Charges for the second billing profile`

|  分層定價 (美元) |數量 | 金額 (美元)|
|---------|---------|---------|
|前 50 TB 的每一 TB 1.84 美元/月    | 50 TB        | 92.0  |
|之後 450 TB 的每一 TB 1.77 美元/月    |  0 TB         | 0.0  |
|總計     |     50 TB  | 92.0

在此案例中，使用 100 TB 資料的費用總計是 **184.0** (92.0 * 2)。

### <a name="azure-reservation-benefits-might-not-apply-to-all-subscriptions"></a>Azure 保留權益可能不會套用至所有訂用帳戶

具有共用範圍的 Azure 保留只會套用至單一帳單設定檔中的訂用帳戶，不會在帳單設定檔之間共用。

![不同計費帳戶結構的保留套用資訊圖形](./media/mca-section-invoice/mca-reservations-benefits-by-bg.png)

在上圖中，Contoso 有兩個訂用帳戶。 Azure 保留權益的套用方式會根據計費帳戶的結構而不同。 在左邊的案例中，保留權益會同時套用到工程帳單設定檔中的兩個計費訂用帳戶。 在右邊的案例中，保留權益只會套用到「訂用帳戶 1」，因為它是工程帳單設定檔中唯一的計費訂用帳戶。

## <a name="check-access-to-a-microsoft-customer-agreement"></a>檢查 Microsoft 客戶合約的存取
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>需要協助嗎？ 請連絡支援人員

如果需要協助，請[連絡支援人員](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解決您的問題。

## <a name="next-steps"></a>後續步驟

- [為 Microsoft 客戶合約建立額外的 Azure 訂用帳戶](create-subscription.md)
- [在 Azure 入口網站中管理計費角色](understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)
- [向其他計費帳戶中的使用者接管 Azure 訂用帳戶的帳單擁有權](mca-request-billing-ownership.md)
