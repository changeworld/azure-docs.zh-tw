---
title: 取得 Microsoft 合作夥伴合約 (MPA) 的 Azure 訂用帳戶的帳單擁有權
description: 了解如何向其他使用者要求 Azure 訂用帳戶的帳單擁有權。
author: amberbhargava
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 06/11/2020
ms.author: banders
ms.openlocfilehash: 28080517a33f1bbf9bee3436493f178b60c0d161
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86523599"
---
# <a name="get-billing-ownership-of-azure-subscriptions-to-your-mpa-account"></a>將 Azure 訂用帳戶的帳單擁有權交給您的 MPA 帳戶

若要為受控服務和 Azure 使用量提供單一合併發票，雲端解決方案提供者 (CSP) 可從具有直接 Enterprise 合約 (EA) 的客戶接管 Azure 訂用帳戶的帳單擁有權。

這項功能僅適用於已認證為 [Azure 專家 MSP](https://partner.microsoft.com/membership/azure-expert-msp) 的 CSP 直接帳單合作夥伴。 這受限於 Microsoft 控管和原則，而且可能需要對特定客戶進行檢閱與核准。

若要要求帳單擁有權，您必須擁有 [全域管理員] 或 [管理員代理人] 角色。 若要深入了解，請參閱[合作夥伴中心 - 指派使用者角色和權限](https://docs.microsoft.com/partner-center/permissions-overview)。

本文適用於 Microsoft 合作夥伴合約的計費帳戶。 針對雲端解決方案提供者 (CSP) 建立這些帳戶，以便在新的商務體驗中管理其客戶的帳單。 新的體驗僅適用於下列合作夥伴：至少有一個客戶已接受 Microsoft 客戶合約 (MCA) 且具有 Azure 方案。 [檢查您是否有 Microsoft 合作夥伴合約的存取權](#check-access-to-a-microsoft-partner-agreement)。

## <a name="prerequisites"></a>必要條件

1. 與客戶建立[經銷商關係](https://docs.microsoft.com/partner-center/request-a-relationship-with-a-customer)。 檢查 [CSP 地區授權概觀](https://docs.microsoft.com/partner-center/regional-authorization-overview)，以確保客戶和合作夥伴租用戶都位於相同的授權區域中。
1. [確認客戶已接受 Microsoft 客戶合約](https://docs.microsoft.com/partner-center/confirm-customer-agreement)。
1. 為客戶設定 [Azure 方案](https://docs.microsoft.com/partner-center/purchase-azure-plan)。 如果客戶透過多個轉銷商購買，您必須為每個客戶與轉銷商組合設定 Azure 方案。

## <a name="request-billing-ownership"></a>要求帳單擁有權

1. 使用 CSP 租用戶中的 CSP 管理代理程式認證登入 [Azure 入口網站](https://portal.azure.com)。
1. 搜尋 [成本管理 + 帳單]。  
    ![顯示在 Azure 入口網站中搜尋 [成本管理 + 計費] 的螢幕擷取畫面](./media/mpa-request-ownership/search-cmb.png)
1. 從左側選取 [客戶]，然後從清單中選取客戶。  
    [![顯示選取客戶的螢幕擷取畫面](./media/mpa-request-ownership/mpa-select-customers.png)](./media/mpa-request-ownership/mpa-select-customers.png#lightbox)
1. 從左下方選取 [轉移要求]，然後選取 [新增要求]。  
    [![顯示選取轉移要求的螢幕擷取畫面](./media/mpa-request-ownership/mpa-select-transfer-requests.png)](./media/mpa-request-ownership/mpa-select-transfer-requests.png#lightbox)
1. 輸入客戶組織中將接受轉移要求之使用者的電子郵件地址。 此使用者必須是 Enterprise 合約上的帳戶擁有者。 選取 [傳送轉移要求]。  
    [![顯示傳送轉移要求的螢幕擷取畫面](./media/mpa-request-ownership/mpa-send-transfer-requests.png)](./media/mpa-request-ownership/mpa-send-transfer-requests.png#lightbox)
1. 使用者會收到一封電子郵件，內含用來檢閱轉移要求的指示。  
    ![顯示檢閱轉移要求電子郵件的螢幕擷取畫面](./media/mpa-request-ownership/mpa-review-transfer-request-email.png)
1. 若要核准轉移要求，使用者須選取電子郵件中的連結，並遵循相關指示。  
    [![顯示檢閱轉移要求的螢幕擷取畫面](./media/mpa-request-ownership/review-transfer-requests.png)](./media/mpa-request-ownership/review-transfer-requests.png#lightbox) 使用者可選取他們要從中轉移 Azure 產品的計費帳戶。 選取之後，會顯示可轉移的合格產品。 **注意：** 已停用的訂用帳戶無法轉移，且會顯示在「無法轉移的 Azure 產品」清單中 (如果適用)。 選取要轉移的 Azure 產品之後，請選取 [驗證]。
1. [傳輸驗證結果] 區域會顯示即將轉移的 Azure 產品所產生的影響。 以下是可能的狀態：
    * **通過** - 此 Azure 產品的驗證已通過，可進行轉移。
    * **警告** - 選取的 Azure 產品出現警告。 雖然產品仍可轉移，但轉移時會造成使用者在想要採取緩和動作時應注意的影響。 例如，假設要轉移的 Azure 訂用帳戶受益於 RI。 轉移之後，訂用帳戶將不再享有該權益。 若要盡可能節省成本，請確定 RI 已與可使用其權益的另一個訂用帳戶相關聯。 實際上，使用者也可選擇返回選取頁面，並取消選取此 Azure 訂用帳戶。
    * **失敗** - 選取的 Azure 產品因錯誤而無法轉移。 使用者必須回到選取頁面並取消選取此產品，以轉移其他已選取的 Azure 產品。  
    ![顯示驗證體驗的螢幕擷取畫面](./media/mpa-request-ownership/validate-transfer-request.png)

## <a name="check-the-transfer-request-status"></a>查看轉移要求狀態

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 搜尋 [成本管理 + 帳單]。  
    ![顯示在 Azure 入口網站中搜尋 [成本管理 + 計費] 的螢幕擷取畫面](./media/mpa-request-ownership/billing-search-cost-management-billing.png)
1. 從左側選取 [客戶]。  
    [![顯示選取客戶的螢幕擷取畫面](./media/mpa-request-ownership/mpa-select-customers.png)](./media/mpa-request-ownership/mpa-select-customers.png#lightbox)
1. 從清單中選取您為其傳送轉移要求的客戶。
1. 從左下方選取 [轉移要求]。 [轉移要求] 頁面會顯示下列資訊：[![顯示轉移要求清單的螢幕擷取畫面](./media/mpa-request-ownership/mpa-select-transfer-requests-for-status.png)](./media/mpa-request-ownership/mpa-select-transfer-requests-for-status.png#lightbox)

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
   |Processing|使用者已核准轉移要求。 使用者所選取的訂用帳戶正在將其帳單移轉至您的帳戶|
   |Completed| 使用者所選取的訂用帳戶已將其帳單轉移至您的帳戶|
   |作業完成，但發生錯誤|要求已完成，但使用者選取的部分訂用帳戶無法轉移其帳單|
   |已過期|使用者未及時接受要求，且已過期|
   |已取消|具有轉移要求存取權的人員已取消要求|
   |已拒絕|使用者已拒絕轉移要求|

1. 選取轉移要求以檢視詳細資料。 [轉移詳細資料] 頁面會顯示下列資訊：[![顯示已轉移訂用帳戶清單的螢幕擷取畫面](./media/mpa-request-ownership/mpa-transfer-completed.png)](./media/mpa-request-ownership/mpa-transfer-completed.png#lightbox)

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

* [Enterprise 開發/測試](https://azure.microsoft.com/offers/ms-azr-0148p/)\*
* [Microsoft Enterprise 合約](https://azure.microsoft.com/pricing/enterprise-agreement/) \(英文\)

\* 您必須透過支援票證將開發/測試訂用帳戶轉換為 EA Enterprise 供應項目。 企業開發/測試訂用帳戶會在移轉後以隨用隨付費率計費。 CSP 合作夥伴將無法使用依據客戶的 EA 透過 Enterprise 開發/測試供應項目所提供的任何折扣。

## <a name="additional-information"></a>其他資訊

下一節將提供其他有關於轉移訂用帳戶的資訊。

### <a name="no-service-downtime"></a>沒有服務停機時間

訂用帳戶中的 Azure 服務可以持續執行，不會發生任何中斷。 我們只會轉換使用者選擇要轉移的 Azure 訂用帳戶的帳務關係。

### <a name="disabled-subscriptions"></a>已停用訂用帳戶

已停用的訂用帳戶無法轉移。 訂用帳戶必須處於作用中狀態，才能轉移其帳單擁有權。

### <a name="azure-resources-transfer"></a>Azure 資源轉移

訂用帳戶中的所有資源 (例如 VM、磁碟和網站) 都會轉移。

### <a name="azure-marketplace-products-transfer"></a>Azure Marketplace 產品轉移

可供雲端解決方案提供者 (CSP) 所管理訂用帳戶使用的 Azure Marketplace 產品會連同其各自的訂用帳戶一起轉移。 訂用帳戶若具有未針對 CSP 啟用的 Azure Marketplace 產品，則無法轉移。

### <a name="azure-reservations-transfer"></a>Azure 保留轉移

Azure 保留不會自動隨著訂用帳戶轉移。 您可以保有 EA 中的保留以用於其他訂用帳戶，或[取消保留](https://docs.microsoft.com/azure/cost-management-billing/reservations/exchange-and-refund-azure-reservations)，讓合作夥伴可在 CSP 中重新購買。

### <a name="access-to-azure-services"></a>對 Azure 服務的存取

使用 [Azure RBAC (角色型存取控制)](../../role-based-access-control/overview.md) 為現有使用者、群組或服務主體指派的存取權，在轉換期間不受影響。 合作夥伴不會取得訂用帳戶的任何新 RBAC 存取權。

合作夥伴應該與客戶合作，以取得訂用帳戶的存取權。 合作夥伴必須取得[代表的管理員 - AOBO](https://channel9.msdn.com/Series/cspdev/Module-11-Admin-On-Behalf-Of-AOBO) 或 [Azure Lighthouse](https://docs.microsoft.com/azure/lighthouse/concepts/cloud-solution-provider) 存取權才能開啟支援票證。

### <a name="azure-support-plan"></a>Azure 支援方案

Azure 支援不會隨著訂用帳戶一起轉移。 如果使用者轉移了所有 Azure 訂用帳戶，請要求他們取消其支援方案。 在轉移之後，CSP 合作夥伴需負責支援。 客戶應該與 CSP 合作夥伴合作以尋求任何支援要求。  

### <a name="charges-for-transferred-subscription"></a>已轉移的訂用帳戶費用

訂用帳戶的原始帳單擁有者須負責處理計算到轉移完成為止的任何費用。 您需負責處理轉移後所報告的費用。 可能有某些費用是在轉移之前產生，但在之後才報告。 這些費用會顯示在您的發票上。

### <a name="cancel-a-transfer-request"></a>取消轉移要求

轉移要求獲得核准或遭到拒絕之前，您可以取消該要求。 若要取消轉移要求，請移至[轉移詳細資料頁面](#check-the-transfer-request-status)，然後從頁面底部選取 [取消]。

### <a name="software-as-a-service-saas-transfer"></a>軟體即服務 (SaaS) 轉移

SaaS 產品不會隨著訂用帳戶一起轉移。 請要求使用者[連絡 Azure 支援](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)，以轉移 SaaS 產品的帳單擁有權。 除了帳單擁有權之外，使用者也可以轉移資源擁有權。 資源擁有權可讓您執行管理作業，例如刪除和檢視產品的詳細資料。 使用者必須是 SaaS 產品的資源擁有者，才能轉移資源擁有權。

### <a name="additional-approval-for-certain-customers"></a>額外核准特定客戶

由於客戶目前的企業註冊結構本質，所以某些客戶轉換要求可能需要與 Microsoft 進行額外的檢閱程序。 合作夥伴嘗試傳送邀請給客戶時，將會收到這類要求的通知。 合作夥伴需要與其合作夥伴開發經理和客戶的帳戶小組合作，以完成此審查程序。

### <a name="azure-subscription-directory"></a>Azure 訂用帳戶目錄

所移轉 Azure 訂用帳戶的目錄必須符合在建立 CSP 關聯性時所選取的客戶目錄。

如果這兩個目錄不相符，就無法移轉訂用帳戶。 您必須藉由選取 Azure 訂用帳戶的目錄來與客戶建立新的 CSP 轉銷商關係，或變更 Azure 訂用帳戶的目錄以符合客戶 CSP 關聯性目錄。 如需詳細資訊，請參閱[將現有的訂用帳戶關聯至您的 Azure AD 目錄](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-how-subscriptions-associated-directory#to-associate-an-existing-subscription-to-your-azure-ad-directory)。

### <a name="ea-subscription-in-the-non-orgnization-directory"></a>非組織目錄中的 EA 訂用帳戶

只要目錄與 CSP 有轉銷商關係，就可以傳送非組織目錄中的 EA 訂用帳戶。 如果目錄沒有轉銷商關聯性，您必須確定目錄中的組織使用者為可以接受合作夥伴關係的「全域管理員」。 使用者名稱的網域名稱部分必須是初始預設網域名稱 "[domain name]. onmicrosoft.com" 或已驗證的非同盟自訂網域名稱，例如 "contoso.com"。  

若要將新的使用者新增至目錄，請參閱[快速入門：將新的使用者新增至 Azure Active Directory 以將新的使用者新增至 AAD 目錄](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory)。

## <a name="check-access-to-a-microsoft-partner-agreement"></a>檢查 Microsoft 合作夥伴合約的存取權

[!INCLUDE [billing-check-mpa](../../../includes/billing-check-mpa.md)]

## <a name="need-help-contact-support"></a>需要協助嗎？ 請連絡支援人員

如果需要協助，請[連絡支援人員](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解決您的問題。

## <a name="next-steps"></a>後續步驟

* Azure 訂用帳戶的帳單擁有權已轉移給您。 請在 [Azure入口網站](https://portal.azure.com)中追蹤這些訂用帳戶的費用。
* 與客戶合作，以取得已轉移 Azure 訂用帳戶的存取權。 [使用 RBAC 管理 Azure 資源的存取](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)。
