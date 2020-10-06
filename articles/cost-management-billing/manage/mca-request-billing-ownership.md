---
title: 取得 Azure 訂用帳戶的帳單擁有權
description: 了解如何向其他使用者要求 Azure 訂用帳戶的帳單擁有權。
author: amberbhargava
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: 2aae71836a4eaba845e4752edf708607977763fa
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91371859"
---
# <a name="get-billing-ownership-of-azure-subscriptions-from-other-accounts"></a>從其他帳戶接管 Azure 訂用帳戶的帳單擁有權

如果現有的帳單擁有者離開您的組織，或您想要透過計費帳戶支付訂用帳戶費用，您可以接管 Azure 訂用帳戶的擁有權。 取得擁有權後，訂用帳戶計費責任會轉移到您的帳戶。

本文適用於 Microsoft 客戶合約的計費帳戶。 請[確認您是否有 Microsoft 客戶合約的存取權](#check-for-access)。

若要要求帳單擁有權，您必須是**發票區段擁有者**或**發票區段參與者**。 若要深入了解，請參閱[發票區段角色工作](understand-mca-roles.md#invoice-section-roles-and-tasks)。

## <a name="request-billing-ownership"></a>要求帳單擁有權

1. 以發票區段擁有者或參與者的身分登入 [Azure 入口網站](https://portal.azure.com)，以存取 Microsoft 客戶合約的計費帳戶。
1. 搜尋 [成本管理 + 帳單]****。  
    ![顯示在 Azure 入口網站中搜尋 [成本管理 + 計費] 的螢幕擷取畫面](./media/mca-request-billing-ownership/billing-search-cost-management-billing.png)
1. 在 [計費範圍] 頁面中選取 [計費帳戶]，以供用來支付訂用帳戶的使用量。 計費帳戶的類型應該是 **Microsoft 客戶合約**。  
    [![顯示在入口網站中搜尋 [成本管理 + 計費] 的螢幕擷取畫面](./media/mca-request-billing-ownership/list-of-scopes.png)](./media/mca-request-billing-ownership/list-of-scopes.png#lightbox)
    > [!NOTE]
    > Azure 入口網站會記住您所存取的最後一個計費範圍，並在您下一次進入 [成本管理 + 帳單] 頁面時顯示該範圍。 如果您先前已造訪過 [成本管理 + 帳單]，就不會看到 [計費範圍] 頁面。 若是如此，請確認您已位於[正確的範圍](#check-for-access)。 如果沒有，請[切換範圍](view-all-accounts.md#switch-billing-scope-in-the-azure-portal)以選取 Microsoft 客戶合約的計費帳戶。
1. 從左側選取 [帳單設定檔]****。  
    [![顯示選取了 [帳單設定檔] 的螢幕擷取畫面](./media/mca-request-billing-ownership/mca-select-profiles.png)](./media/mca-request-billing-ownership/mca-select-profiles.png#lightbox)
    > [!Note]
    > 如果您沒有看到 [帳單設定檔]，則表示您不在正確的計費範圍。 您必須選取 Microsoft 客戶合約的計費帳戶，然後選取 [帳單設定檔]。 若要了解如何變更範圍，請參閱[在 Azure 入口網站中切換計費範圍](view-all-accounts.md#switch-billing-scope-in-the-azure-portal)。
1. 從清單中選取 [帳單設定檔]****。 在您接管訂用帳戶的擁有權後，其使用量費用就會計入此帳單設定檔。
1. 從左側選取 [發票區段]****。  
    [![顯示選取發票區段的螢幕擷取畫面](./media/mca-request-billing-ownership/mca-select-invoice-sections.png)](./media/mca-request-billing-ownership/mca-select-invoice-sections.png#lightbox)   
1. 從清單中選取發票區段。 在您接管訂用帳戶的擁有權後，其使用量就會指派給帳單設定檔發票的這個區段。
1. 從左下方選取 [轉移要求]****，然後選取 [新增要求]****。  
    [![顯示選取轉移要求的螢幕擷取畫面](./media/mca-request-billing-ownership/mca-select-transfer-requests.png)](./media/mca-request-billing-ownership/mca-select-transfer-requests.png#lightbox)
1. 輸入您要對其要求帳單擁有權之使用者的電子郵件地址。 這個使用者必須是 Microsoft 線上服務方案計費帳戶的帳戶管理員，或是Enterprise 合約的帳戶擁有者。 如需詳細資訊，請參閱[在 Azure 入口網站中檢視計費帳戶](view-all-accounts.md)。 選取 [傳送轉移要求]****。  
    [![顯示傳送轉移要求的螢幕擷取畫面](./media/mca-request-billing-ownership/mca-send-transfer-requests.png)](./media/mca-request-billing-ownership/mca-send-transfer-requests.png#lightbox)
1. 使用者會收到一封電子郵件，內含用來檢閱轉移要求的指示。  
    ![顯示檢閱轉移要求電子郵件的螢幕擷取畫面](./media/mca-request-billing-ownership/mca-review-transfer-request-email.png)
1. 若要核准轉移要求，使用者須選取電子郵件中的連結，並遵循相關指示。
    [![顯示檢閱轉移要求的螢幕擷取畫面](./media/mca-request-billing-ownership/review-transfer-requests.png)](./media/mca-request-billing-ownership/review-transfer-requests.png#lightbox) 使用者可選取他們要從中轉移 Azure 產品的計費帳戶。 選取之後，會顯示可轉移的合格產品。 **注意：** 已停用的訂用帳戶無法轉移，且會顯示在「無法轉移的 Azure 產品」清單中 (如果適用)。 選取要轉移的 Azure 產品之後，請選取 [驗證]****。
1. [傳輸驗證結果]**** 區域會顯示即將轉移的 Azure 產品所產生的影響。 以下是可能的狀態：
    * **通過** - 此 Azure 產品的驗證已通過，可進行轉移。
    * **警告** - 選取的 Azure 產品出現警告。 雖然產品仍可轉移，但轉移時會造成使用者在想要採取緩和動作時應注意的影響。 例如，假設要轉移的 Azure 訂用帳戶受益於 RI。 轉移之後，訂用帳戶將不再享有該權益。 若要盡可能節省成本，請確定 RI 已與可使用其權益的另一個訂用帳戶相關聯。 實際上，使用者也可選擇返回選取頁面，並取消選取此 Azure 訂用帳戶。
    * **失敗** - 選取的 Azure 產品因錯誤而無法轉移。 使用者必須回到選取頁面並取消選取此產品，以轉移其他已選取的 Azure 產品。  
    ![顯示驗證體驗的螢幕擷取畫面](./media/mca-request-billing-ownership/validate-transfer-request.png)

## <a name="check-the-transfer-request-status"></a>查看轉移要求狀態

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 搜尋 [成本管理 + 帳單]****。  
    ![顯示在 Azure 入口網站中搜尋 [成本管理 + 計費] 的螢幕擷取畫面](./media/mca-request-billing-ownership/billing-search-cost-management-billing.png)
1. 在 [計費範圍] 頁面中，選取要為其傳送移轉要求的計費帳戶。
1. 從左側選取 [帳單設定檔]****。  
    [![顯示選取了 [帳單設定檔] 的螢幕擷取畫面](./media/mca-request-billing-ownership/mca-select-profiles.png)](./media/mca-request-billing-ownership/mca-select-profiles.png#lightbox)
1. 選取已為其傳送移轉要求的 [帳單設定檔]****。
1. 從左側選取 [發票區段]****。  
    [![顯示選取發票區段的螢幕擷取畫面](./media/mca-request-billing-ownership/mca-select-invoice-sections.png)](./media/mca-request-billing-ownership/mca-select-invoice-sections.png#lightbox)   
1. 從清單中選取已為其傳送移轉要求的發票區段。
1. 從左下方選取 [轉移要求]****。 [轉移要求] 頁面會顯示下列資訊：  
    [![顯示轉移要求清單的螢幕擷取畫面](./media/mca-request-billing-ownership/mca-select-transfer-requests-for-status.png)](./media/mca-request-billing-ownership/mca-select-transfer-requests-for-status.png#lightbox)
   |資料行|定義|
   |---------|---------|
   |要求日期|轉移要求的傳送日期|
   |收件者|您傳送轉移帳單擁有權要求的收件方電子郵件地址|
   |到期日期|要求的到期日|
   |狀態|轉移要求的狀態|

    轉移要求可處於下列其中一種狀態：

   |狀態|定義|
   |---------|---------|
   |進行中|使用者尚未接受轉移要求|
   |Processing|使用者已核准轉移要求。 使用者所選取的訂用帳戶正在將其帳單移轉至您的發票區段|
   |Completed| 使用者所選取的訂用帳戶已將其帳單轉移至您的發票區段|
   |作業完成，但發生錯誤|要求已完成，但使用者選取的部分訂用帳戶無法轉移其帳單|
   |已過期|使用者未及時接受要求，且已過期|
   |已取消|具有轉移要求存取權的人員已取消要求|
   |已拒絕|使用者已拒絕轉移要求|

1. 選取轉移要求以檢視詳細資料。 [轉移詳細資料] 頁面會顯示下列資訊：  
    [![顯示已轉移訂用帳戶清單的螢幕擷取畫面](./media/mca-request-billing-ownership/mca-transfer-completed.png)](./media/mca-request-billing-ownership/mca-transfer-completed.png#lightbox)

   |資料行  |定義|
   |---------|---------|
   |轉移要求識別碼|轉移要求的唯一識別碼。 如果您提交支援要求，請將識別碼提供給 Azure 支援，以加速支援要求的處理|
   |要求轉移時間|轉移要求的傳送日期|
   |要求轉移者|傳送轉移要求的使用者電子郵件地址|
   |轉移要求到期時間| 轉移要求的到期日|
   |收件者的電子郵件地址|您傳送轉移帳單擁有權要求的收件方電子郵件地址|
   |傳送給收件者的轉移連結|傳送給使用者供其檢閱轉移要求的 URL|

## <a name="supported-subscription-types"></a>支援的訂用帳戶類型

您可以要求下列訂用帳戶類型的帳單擁有權。

- [行動套件](https://azure.microsoft.com/offers/ms-azr-0025p/)\*
- [Azure in Open 授權](https://azure.microsoft.com/offers/ms-azr-0111p/)\*
- [Azure Pass 贊助](https://azure.microsoft.com/offers/azure-pass/)\*
- [Enterprise 開發/測試](https://azure.microsoft.com/offers/ms-azr-0148p/)
- [免費試用](https://azure.microsoft.com/offers/ms-azr-0044p/)\*
- [隨用隨付](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [隨用隨付開發/測試](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Microsoft Azure 方案](https://azure.microsoft.com/offers/ms-azr-0017g/)\*\*
- [Microsoft Azure 贊助的供應項目](https://azure.microsoft.com/offers/ms-azr-0036p/)\*
- [Microsoft Enterprise 合約](https://azure.microsoft.com/pricing/enterprise-agreement/) \(英文\)
- [Microsoft 客戶合約](https://azure.microsoft.com/pricing/purchase-options/microsoft-customer-agreement/)
- [Microsoft 合作夥伴網路](https://azure.microsoft.com/offers/ms-azr-0025p/)\*
- [MSDN 平台](https://azure.microsoft.com/offers/ms-azr-0062p/)\*
- [Visual Studio Enterprise (BizSpark) 訂閱者](https://azure.microsoft.com/offers/ms-azr-0064p/)\*
- [Visual Studio Enterprise (MPN) 訂閱者](https://azure.microsoft.com/offers/ms-azr-0029p/)\*
- [Visual Studio Enterprise 訂閱者](https://azure.microsoft.com/offers/ms-azr-0063p/)\*
- [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)\*
- [Visual Studio Test Professional 訂閱者](https://azure.microsoft.com/offers/ms-azr-0060p/)\*

\* 在轉移之後，訂用帳戶上任何可用的點數在新帳戶中都將無法使用。

\*\* 僅支援在 Azure 網站註冊期間在帳戶中建立的訂用帳戶。

## <a name="additional-information"></a>其他資訊

下一節將提供其他有關於轉移訂用帳戶的資訊。

### <a name="no-service-downtime"></a>沒有服務停機時間

訂用帳戶中的 Azure 服務可以持續執行，不會發生任何中斷。 我們只會轉換使用者選擇要轉移的 Azure 訂用帳戶的帳務關係。

### <a name="disabled-subscriptions"></a>已停用訂用帳戶

已停用的訂用帳戶無法轉移。 訂用帳戶必須處於作用中狀態，才能轉移其帳單擁有權。

### <a name="azure-resources-transfer"></a>Azure 資源轉移

訂用帳戶中的所有資源 (例如 VM、磁碟和網站) 都會轉移。

### <a name="azure-marketplace-products-transfer"></a>Azure Marketplace 產品轉移

Azure Marketplace 產品會連同其各自的訂用帳戶一起轉移。

### <a name="azure-reservations-transfer"></a>Azure 保留轉移

如果您要轉移 Enterprise 合約 (EA) 訂用帳戶或 Microsoft 客戶合約，Azure 保留不會自動與訂用帳戶一起移轉。 若要移轉保留，請[連絡 Azure 支援](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

### <a name="access-to-azure-services"></a>對 Azure 服務的存取

使用 [Azure 角色型存取控制 (Azure RBAC)](../../role-based-access-control/overview.md) 為現有使用者、群組或服務主體指派的存取權，在轉換期間不受影響。

### <a name="azure-support-plan"></a>Azure 支援方案

Azure 支援不會隨著訂用帳戶一起轉移。 如果使用者轉移了所有 Azure 訂用帳戶，請要求他們取消其支援方案。

### <a name="charges-for-transferred-subscription"></a>已轉移的訂用帳戶費用

訂用帳戶的原始帳單擁有者須負責處理計算到轉移完成為止的任何費用。 您的發票區段會處理轉移後所報告的費用。 可能有某些費用是在轉移之前產生，但在之後才報告。 這些費用會顯示在您的發票區段上。

### <a name="cancel-a-transfer-request"></a>取消轉移要求

轉移要求獲得核准或遭到拒絕之前，您可以取消該要求。 若要取消轉移要求，請移至[轉移詳細資料頁面](#check-the-transfer-request-status)，然後從頁面底部選取 [取消]。

### <a name="software-as-a-service-saas-transfer"></a>軟體即服務 (SaaS) 轉移

SaaS 產品不會隨著訂用帳戶一起轉移。 請要求使用者[連絡 Azure 支援](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)，以轉移 SaaS 產品的帳單擁有權。 除了帳單擁有權之外，使用者也可以轉移資源擁有權。 資源擁有權可讓您執行管理作業，例如刪除和檢視產品的詳細資料。 使用者必須是 SaaS 產品的資源擁有者，才能轉移資源擁有權。

## <a name="check-for-access"></a>檢查存取權
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>需要協助嗎？ 請連絡支援人員

如果需要協助，請[連絡支援人員](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解決您的問題。

## <a name="next-steps"></a>後續步驟

- Azure 訂用帳戶的帳單擁有權已轉移至您的發票區段。 請在 [Azure入口網站](https://portal.azure.com)中追蹤這些訂用帳戶的費用。
- 授與他人對這些訂用帳戶檢視和管理帳單的權限。 如需詳細資訊，請參閱[發票區段角色和工作](understand-mca-roles.md#invoice-section-roles-and-tasks)。
