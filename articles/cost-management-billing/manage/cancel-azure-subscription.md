---
title: 取消 Azure 訂用帳戶
description: 說明如何取消 Azure 訂用帳戶，例如免費試用訂用帳戶
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 12/14/2020
ms.author: banders
ms.openlocfilehash: 662e1bf721c93ee1d59946d2fd603551f3f88ad3
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/15/2020
ms.locfileid: "97503789"
---
# <a name="cancel-your-azure-subscription"></a>取消 Azure 訂用帳戶

如果您不再需要訂用帳戶，您可以在 Azure 入口網站中取消您的 Azure 訂用帳戶。

雖然並非必要，但 Microsoft *建議* 您在取消訂用帳戶之前採取下列動作：

* 備份您的資料。 例如，如果您將資料儲存在 Azure 儲存體或 SQL，請下載複本。 如果您有虛擬機器，請將其映像儲存在本機。
* 關閉您的服務。 移至 [管理入口網站中的資源頁面](https://ms.portal.azure.com/?flight=1#blade/HubsExtension/Resources/resourceType/Microsoft.Resources%2Fresources)，並 **停止** 任何執行中的虛擬機器、應用程式或其他服務。
* 考慮移轉您的資料。 請參閱[將資源移動到新的資源群組或訂用帳戶](../../azure-resource-manager/management/move-resource-group-and-subscription.md)。
* 刪除所有資源和所有資源群組。
* 如果您在`AssignableScopes`中有任何參考此訂用帳戶的自訂角色，您應該更新這些自訂角色以移除訂用帳戶。 如果您嘗試在取消訂用帳戶之後更新自訂角色，可能會收到錯誤。 如需詳細資訊，請參閱[針對自訂角色的問題進行疑難排解](../../role-based-access-control/troubleshooting.md#problems-with-custom-roles)和 [Azure 自訂角色](../../role-based-access-control/custom-roles.md)。

如果您取消付費 Azure 支援方案，剩餘的訂閱期間仍會計費。 如需詳細資訊，請參閱 [Azure 支援方案](https://azure.microsoft.com/support/plans/)。

## <a name="who-can-cancel-a-subscription"></a>誰可以取消訂用帳戶？

下表描述取消訂用帳戶所需的權限。

|訂用帳戶類型     |誰可以取消  |
|---------|---------|
|當您透過 Azure 網站註冊 Azure 時所建立的訂用帳戶。 例如，當您註冊 [Azure 免費帳戶](https://azure.microsoft.com/offers/ms-azr-0044p/)、[採用隨用隨付費率的帳戶](https://azure.microsoft.com/offers/ms-azr-0003p/)，或以 [Visual studio 訂閱者](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/)身分註冊時。 |  帳戶管理員和訂用帳戶的擁有者  |
|[Microsoft Enterprise 合約](https://azure.microsoft.com/pricing/enterprise-agreement/)與 [Enterprise 開發/測試](https://azure.microsoft.com/offers/ms-azr-0148p/)     |  帳戶擁有者和訂用帳戶的擁有者       |
|[Azure 方案](https://azure.microsoft.com/offers/ms-azr-0017g/)和 [適用於 DevTest 的 Azure 方案](https://azure.microsoft.com/offers/ms-azr-0148g/)     |  訂用帳戶的擁有者      |


## <a name="cancel-subscription-in-the-azure-portal"></a>在 Azure 入口網站中取消訂用帳戶

1. 在 [Azure 入口網站的 [訂用帳戶] 頁面](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)中選取您的訂用帳戶。
1. 選取您要取消的訂用帳戶。
1. 選取[概觀]，然後選取 [取消訂用帳戶]。
    ![顯示 [取消] 按鈕的螢幕擷取畫面](./media/cancel-azure-subscription/cancel_ibiza.png)
1. 遵循提示並完成取消作業。

> [!NOTE]
> 如果客戶要求，或發生未付款或詐騙的狀況，合作夥伴可以暫停或取消訂用帳戶。 如需詳細資訊，請參閱[暫停或取消訂用帳戶](/partner-center/create-a-new-subscription#suspend-or-cancel-a-subscription)。

## <a name="cancel-a-support-plan"></a>取消支援方案

如果您已透過 Azure 網站、Azure 入口網站購買支援方案，或如果您的 Microsoft 客戶合約下有支援方案，您可以將支援方案取消。 如果您是透過 Microsoft 業務代表或合作夥伴購買支援方案，請向他們尋求協助。 

1. 在 Azure 入口網站中，瀏覽至 [成本管理 + 計費]。
1. 在 [帳單] 底下，選取 [週期性費用]。
1. 在支援方案明細的右側，選取省略符號 ()，然後選取 [關閉自動續約]。

## <a name="what-happens-after-subscription-cancellation"></a>取消訂用帳戶後會發生什麼情況？

在您取消之後，計費會立即停止。 不過，最多可能需要 10 分鐘，取消作業才會在入口網站中顯示。 如果您在計費期間中途取消訂用帳戶，我們會在該期間結束後的一般發票核發日傳送最後一張發票。

在您取消之後，您的服務就會停用。 這表示系統會解除配置您的虛擬機器、釋出暫時 IP 位址，且儲存體會變成唯讀。

訂用帳戶取消後，Microsoft 會等 30 到 90 天之後才永久刪除您的資料，以防您需要存取資料，或您改變心意。 我們不會向您收取保留資料的費用。 若要深入了解，請參閱 [Microsoft 信任中心 - 我們如何管理您的資料](https://go.microsoft.com/fwLink/p/?LinkID=822930&clcid=0x409) \(英文\)。

## <a name="delete-free-trial-subscription"></a>刪除免費試用訂用帳戶

如果您有免費試用訂用帳戶，則不需要等待 30 天的時間，訂用帳戶就會自動刪除。 在取消訂用帳戶後經過 *三天*，就可以將其刪除。 在您取消訂用帳戶後，要經過三天才可使用 [刪除訂用帳戶] 選項。

1. 因此，請在取消訂用帳戶後等候三天的時間。
1. 在 Azure 入口網站的 [[訂用帳戶](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)] 頁面上選取您的訂用帳戶。
1. 選取您要刪除的訂用帳戶。
1. 選取[概觀]，然後選取 [刪除訂用帳戶]。

## <a name="reactivate-a-subscription"></a>重新啟用訂用帳戶

如果您不小心取消採用隨用隨付費率的訂用帳戶，您可以[在帳戶中心重新啟動它](subscription-disabled.md)。

如果您的訂用帳戶不是採用隨用隨付費率的訂用帳戶，請在取消後的 90 天內與支援服務連絡，以重新啟用您的訂用帳戶。

## <a name="why-dont-i-see-the-cancel-subscription-option-on-the-azure-portal"></a>為何我在 Azure 入口網站上看不到 [取消訂用帳戶] 選項？ 

您可能沒有取消訂用帳戶所需的權限。 請參閱[誰可以取消訂用帳戶？](#who-can-cancel-a-subscription)，了解哪些人可取消不同類型的訂用帳戶。

## <a name="how-do-i-delete-my-azure-account"></a>如何刪除我的 Azure 帳戶？

*我需要移除帳戶，包括我所有的個人資訊在內。我已取消了我的作用中 (免費試用) 訂用帳戶。我沒有任何作用中的訂用帳戶，且想要完全刪除我的帳戶*。

* 如果您透過組織而擁有 Azure Active Directory 帳戶，則 Azure AD 系統管理員可以刪除該帳戶。 在這之後，您的服務將會停用。 這表示系統會解除配置您的虛擬機器、釋出暫時 IP 位址，且儲存體會變成唯讀。 總之，當您取消之後，計費會立即停止。

* 如果您沒有透過組織取得的 Azure AD 帳戶，則可以自行取消並刪除您的 Azure 訂用帳戶，然後從該帳戶中移除您的信用卡。 此動作不會刪除帳戶，但會使其無法運作。 如果相關聯的 Microsoft 帳戶未用於其他用途，您可以進一步同時將其刪除。

## <a name="how-do-i-cancel-a-visual-studio-professional-account"></a>如何取消 Visual Studio Professional 帳戶？

請參閱[續約和取消](/visualstudio/subscriptions/faq/admin/renewal-cancellation)一文。 如果您有任何 Visual Studio 的 Azure 訂用帳戶，則必須一併加以取消和刪除。

## <a name="next-steps"></a>後續步驟

- 如有需要，您可以在[帳戶中心](subscription-disabled.md)重新啟用隨用隨付訂用帳戶。