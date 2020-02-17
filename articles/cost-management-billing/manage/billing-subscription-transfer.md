---
title: 轉移 Azure 訂用帳戶的帳單擁有權
description: 說明如何將 Azure 訂用帳戶的帳單擁有權轉移給另一位帳戶，以及一些關於此程序的常見問題集 (FAQ)
keywords: 轉移 azure 訂用帳戶,azure 轉移訂用帳戶,將 azure 訂用帳戶移到另一個帳戶,azure 變更訂用帳戶擁有者,將 azure 訂用帳戶轉移給另一個帳戶, azure 轉移計費
author: bandersmsft
ms.reviewer: amberb
tags: billing,top-support-issue
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3eb76535a8047840a577f5b044001d85c20b13f0
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2020
ms.locfileid: "77188397"
---
# <a name="transfer-billing-ownership-of-an-azure-subscription-to-another-account"></a>將 Azure 訂用帳戶的帳單擁有權轉移給另一個帳戶

如果您要離開組織，或想要向另一個帳戶收取訂用帳戶費用，您可以轉移 Azure 訂用帳戶的帳單擁有權。 將帳單擁有權轉移至另一個帳戶，可為新帳戶中的管理員提供計費工作的權限。 他們可以變更付款方式、查看費用，以及取消訂用帳戶。

如果您想要保留帳單擁有權，但變更訂用帳戶的類型，請參閱[將 Azure 訂用帳戶切換為其他供應項目](switch-azure-offer.md)。 若要控制有誰可以管理訂用帳戶中的資源，請參閱 [Azure 資源的內建角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)。

如果您是 Enterprise 合約 (EA) 客戶，您的企業系統管理員可以在帳戶之間轉移訂用帳戶的帳單擁有權。 如需詳細資訊，請參閱[轉移 Enterprise 合約 (EA) 訂用帳戶的帳單擁有權](#EA)。

## <a name="transfer-billing-ownership-of-an-azure-subscription"></a>轉移 Azure 訂用帳戶的帳單擁有權

1. 以計費帳戶 (具有您要轉移的訂用帳戶) 的管理員身分登入 [Azure 入口網站](https://portal.azure.com)。 若要找出您是否為管理員，請參閱[常見問題集](#faq)。

1. 針對 [成本管理 + 帳單]  進行搜尋。

   ![顯示 Azure 入口網站搜尋的螢幕擷取畫面](./media/billing-subscription-transfer/billing-search-cost-management-billing.png)

1. 從左側窗格中選取 [訂用帳戶]  。 視存取權而定，您可能需要選取計費範圍，然後選取 [訂用帳戶]  或 [Azure 訂用帳戶]  。

1. 針對您要轉移的訂用帳戶選取 [轉移帳單擁有權]  。

   ![選取要轉移的訂用帳戶](./media/billing-subscription-transfer/billing-select-subscription-to-transfer.png)

1. 輸入使用者的電子郵件地址，該使用者是將成為訂用帳戶新擁有者之帳戶的計費管理員。

1. 如果您要將訂用帳戶轉移至另一個 Azure AD 租用戶中的帳戶，請選取是否要將訂用帳戶移至新帳戶的租用戶。 如需詳細資訊，請參閱[將訂用帳戶轉移給另一個 Azure AD 租用戶中的帳戶](#transfer-a-subscription-to-another-azure-ad-tenant-account)。

    > [!IMPORTANT]
    >
    > 如果您選擇將訂用帳戶移至新帳戶的 Azure AD 租用戶，則會永久移除訂用帳戶中用以管理資源的所有[角色型存取控制 (RBAC)](../../role-based-access-control/overview.md) 指派。 只有新帳戶中接受轉移要求的使用者，才有權利管理訂用帳戶中的資源。 如需詳細資訊，請參閱[將訂用帳戶轉移給另一個 Azure AD 租用戶中的使用者](../../cognitive-services/acoustics/known-issues.md)。 或者，您可以取消核取 Azure AD 租用戶要轉移帳單擁有權的 [訂用帳戶] 方塊，而不需將訂用帳戶移至新帳戶的租用戶。 如果您這麼做，將會維護用以管理 Azure 資源的現有 RBAC 權限。

    ![傳送轉移頁面](./media/billing-subscription-transfer/billing-send-transfer-request.PNG)

1. 選取 [傳送轉移要求]  。

1. 使用者會收到一封電子郵件，內含用來檢閱轉移要求的指示。

   ![傳送給接受者的訂用帳戶移轉電子郵件](./media/billing-subscription-transfer/billing-receiver-email.png)

1. 若要核准轉移要求，使用者須選取電子郵件中的連結，並遵循相關指示。 接著，使用者會選取將用來支付訂用帳戶的付款方式。 如果使用者沒有 Azure 帳戶，他們就必須註冊新帳戶。

   ![第一個訂用帳戶移轉網頁](./media/billing-subscription-transfer/billing-accept-ownership-step1.png)

   ![第二個訂用帳戶移轉網頁](./media/billing-subscription-transfer/billing-accept-ownership-step2.png)

   ![第二個訂用帳戶移轉網頁](./media/billing-subscription-transfer/billing-accept-ownership-step3.png)

1. 成功！ 現在已移轉訂用帳戶。

## <a name="transfer-a-subscription-to-another-azure-ad-tenant-account"></a>將訂用帳戶轉移給另一個 Azure AD 租用戶帳戶

當您註冊 Azure 時，系統會為您建立 Azure Active Directory (AD) 租用戶。 此租用戶代表您的帳戶。 您可以使用此租用戶來管理訂用帳戶和資源的存取權。

當您建立新的訂用帳戶時，該帳戶會裝載於您帳戶的 Azure AD 租用戶中。 如果您想要將訂用帳戶或其資源的存取權提供給其他人，則必須邀請他們加入您的租用戶。 這麼做可協助您控制訂用帳戶和資源的存取權。

當您將訂用帳戶的帳單擁有權轉移給另一個 Azure AD 租用戶中的帳戶時，您可以將訂用帳戶移至新帳戶的租用戶。 如果您這麼做，所有具有[角色型存取 (RBAC)](../../role-based-access-control/role-assignments-portal.md) 來管理訂用帳戶及其資源的使用者、群組或服務主體都會失去其存取權。 只有新帳戶中接受轉移要求的使用者，才有權利管理資源。 新的擁有者必須[以手動方式將這些使用者新增至訂用帳戶](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)，以將存取權提供給失去存取權的人員。


## <a name="transfer-visual-studio-and-partner-network-subscriptions"></a>轉移 Visual Studio 和合作夥伴網路訂用帳戶

Visual Studio 和 Microsoft 合作夥伴網路訂用帳戶都有相關聯的每月週期性 Azure 點數。 當您轉移這些訂用帳戶時，點數無法在目的地計費帳戶中使用。 訂用帳戶會在目的地計費帳戶中使用此點數。 例如，如果 Bob 在 9 月 9 日將 Visual Studio Enterprise 訂用帳戶轉移給 Jane 的帳戶，且 Jane 接受轉移。 完成轉移之後，訂用帳戶會開始在 Jane 的帳戶中使用點數。 點數將會在每個月第九天重設。


<a id="EA"></a>

## <a name="transfer-ea-subscription-billing-ownership"></a>轉移 EA 訂用帳戶的帳單擁有權

企業系統管理員可以在註冊內的帳戶之間轉移訂用帳戶的擁有權。 如需詳細資訊，請參閱 EA 入口網站中的[變更帳戶擁有者](https://docs.microsoft.com/azure/cost-management-billing/manage/ea-portal-get-started#change-account-owner)。

## <a name="next-steps-after-accepting-billing-ownership"></a>接受帳單擁有權後的後續步驟

如果您已接受 Azure 訂用帳戶的帳單擁有權，建議您檢閱下列後續步驟：

1. 請檢閱並更新服務管理員、共同管理員和其他 RBAC 角色。 若要深入了解，請參閱[新增或變更 Azure 訂用帳戶管理員](add-change-subscription-administrator.md)和[使用 RBAC 和 Azure 入口網站來管理存取權](../../role-based-access-control/role-assignments-portal.md)。
1. 更新與此訂用帳戶服務相關聯的認證，包括：
   1. 可將使用者管理權限授與給訂用帳戶資源的管理憑證。 如需詳細資訊，請參閱 [建立和上傳 Azure 的管理憑證](../../cloud-services/cloud-services-certs-create.md)
   1. 服務 (例如儲存體) 的存取金鑰。 如需詳細資訊，請參閱[關於 Azure 儲存體帳戶](../../storage/common/storage-create-storage-account.md)
   1. 服務 (例如 Azure 虛擬機器) 的遠端存取認證。
1. 如果您正與合作夥伴協力作業，請考慮更新此訂用帳戶的合作夥伴 ID。 您可以在 [Azure 入口網站](https://portal.azure.com)更新合作夥伴識別碼。 如需詳細資訊，請參閱[將合作夥伴識別碼連結到您的 Azure 帳戶](link-partner-id.md)。

<a id="supported"></a>

## <a name="supported-subscription-types"></a>支援的訂用帳戶類型

Azure 入口網站中的訂用帳戶轉移適用於以下所列的訂用帳戶類型。 [免費試用](https://azure.microsoft.com/offers/ms-azr-0044p/) 或 [Azure in Open (AIO)](https://azure.microsoft.com/offers/ms-azr-0111p/) 訂用帳戶目前不支援轉移。 如需因應措施，請參閱[將資源移動到新的資源群組或訂用帳戶](../../azure-resource-manager/management/move-resource-group-and-subscription.md)。 若要轉移[贊助](https://azure.microsoft.com/offers/ms-azr-0036p/)或支援方案等其他訂用帳戶，[請連絡 Azure 支援](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

- [Enterprise 合約 (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)\*
- [Microsoft 合作夥伴網路](https://azure.microsoft.com/offers/ms-azr-0025p/)  
- [Visual Studio Enterprise (MPN) 訂閱者](https://azure.microsoft.com/offers/ms-azr-0029p/)
- [MSDN 平台](https://azure.microsoft.com/offers/ms-azr-0062p/)  
- [隨用隨付](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [隨用隨付開發/測試](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/)
- [Visual Studio Enterprise：BizSpark](https://azure.microsoft.com/offers/ms-azr-0064p/)
- [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)
- [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/)
- [Microsoft Azure 方案](https://azure.microsoft.com/offers/ms-azr-0017g/)\*\*

\*[透過 EA 入口網站](#EA)。

\*\* 僅對在 Azure 網站註冊期間建立的帳戶提供支援。

<a id="faq"></a>

## <a name="frequently-asked-questions-faq-for-senders"></a>傳送者的常見問題集 (FAQ)

這些常見問題集適用於將 Azure 訂用帳戶的帳單擁有權轉移給另一個帳戶的使用者。

### <a name="whoisaa"></a> 誰是帳戶的計費管理員？

計費管理員是有權管理帳戶計費的人員。 他們已獲准存取 [Azure 入口網站](https://portal.azure.com)上的計費，並可執行各種計費工作，例如建立訂用帳戶、檢視和支付發票，或更新付款方式。

若要識別您身為計費管理員的帳戶，請使用下列步驟：

1. 瀏覽 [Azure 入口網站中的 [成本管理 + 帳單] 頁面](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/Overview)。
1. 從左側窗格中選取 [所有計費範圍]  。
1. [訂用帳戶] 頁面會列出您在其中是計費管理員的所有訂用帳戶。

如果您不確定誰是訂用帳戶的帳戶管理員，請使用下列步驟來找出帳戶管理員。

1. 瀏覽 [Azure 入口網站中的 [訂用帳戶] 頁面](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)。
1. 選取您想要檢查的訂用帳戶，然後查看 [設定]  。
1. 選取 [屬性]  。 該訂用帳戶的帳戶管理員會顯示在 [帳戶管理員]  方塊中。

### <a name="does-everything-transfer-including-resource-groups-vms-disks-and-other-running-services"></a>所有項目都會移轉嗎？ 包括資源群組、VM、磁碟和其他執行中的服務？

VM、磁碟和網站等所有資源都會轉移給新的帳戶。 不過，如果您將訂用帳戶轉移給另一個 Azure AD 租用戶中的帳戶，並[不會轉移](#transfer-a-subscription-to-another-azure-ad-tenant-account)訂用帳戶上的任何[管理員角色](add-change-subscription-administrator.md)和[角色型存取控制 (RBAC)](../../role-based-access-control/role-assignments-portal.md) 指派。 此外，[應用程式註冊](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md)和其他租用戶特定服務也不會隨訂用帳戶轉移。

### <a name="can-i-transfer-ownership-to-an-account-in-another-country"></a>我可以將擁有權轉移給另一個國家/地區的帳戶嗎？
很抱歉，您無法在 Azure 入口網站中執行跨國家/地區的轉移。 若要跨國家/地區轉移訂用帳戶，[請連絡支援人員](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

### <a name="i-am-an-administrator-on-two-accounts-can-i-transfer-a-subscription-from-one-of-my-accounts-to-another"></a>我是兩個帳戶的管理員。 我可以將訂用帳戶從我的帳戶轉移給另一個帳戶嗎？
是，您可以在您的帳戶之間轉移訂用帳戶。 您的帳戶在概念上會視為兩個不同使用者的帳戶，因此您可以使用上述步驟，在您的帳戶轉移訂用帳戶。

### <a name="does-a-subscription-transfer-result-in-any-service-downtime"></a>移轉訂閱會造成服務中斷嗎？

如果您將訂用帳戶轉移至相同 Azure Active Directory 租用戶中的帳戶，並不會影響在訂用帳戶中執行的資源。 不過，儲存在 PowerShell 中的內容資訊並不會更新，因此您可能必須加以清除或變更設定。 如果您將訂用帳戶轉移到另一個租用戶中的帳戶，並決定將訂用帳戶移至該租用戶，則具有[角色型存取 (RBAC)](../../role-based-access-control/overview.md) 可管理訂用帳戶中資源的所有使用者、群組和服務主體都會失去其存取權。 這可能會造成服務停機。

### <a name="can-users-in-new-account-access-usage-and-billing-history"></a>新帳戶中的使用者可以存取使用量和計費記錄嗎？

新帳戶中的使用者唯一可用的資訊是您訂用帳戶的上個月成本。 其餘的使用量及帳單記錄不會隨著訂用帳戶一起移轉

### <a name="how-do-i-migrate-data-and-services-for-my-azure-subscription-to-new-subscription"></a>如何將 Azure 訂閱的資料與服務移轉到新的訂閱？

如果您無法轉移訂用帳戶的擁有權，則可手動移轉您的資源。 請參閱[將資源移動到新的資源群組或訂用帳戶](../../azure-resource-manager/management/move-resource-group-and-subscription.md)。

### <a name="if-i-transfer-a-visual-studio-or-microsoft-partner-network-subscription-does-my-credit-carry-forward-with-the-subscription-in-the-new-account"></a>如果我轉移 Visual Studio 或 Microsoft 合作夥伴網路訂用帳戶，我的點數是否會隨著新帳戶中的訂用帳戶一起結轉？

否，您的點數無法在新帳戶中使用。 接受傳輸要求的使用者必須具有 Visual Studio 授權，才能接受轉移要求。 訂用帳戶會使用使用者帳戶中可用的 Visual Studio 點數。 如需詳細資訊，請參閱[轉移 Visual Studio 和合作夥伴網路訂用帳戶](#transfer-visual-studio-and-partner-network-subscriptions)。


## <a name="frequently-asked-questions-faq-for-recipients"></a>接受者的常見問題集 (FAQ)

這些常見問題集適用於從另一個帳戶接受 Azure 訂用帳戶帳單擁有權的使用者。

### <a name="if-i-take-over-billing-ownership-of-a-subscription-from-another-account-do-users-in-that-account-continue-to-have-access-to-my-resources"></a>如果我從另一個帳戶接收訂用帳戶的帳單擁有權，該帳戶中的使用者是否能繼續存取我的資源？

是。 不過，[管理員角色](add-change-subscription-administrator.md)和[角色型存取控制 (RBAC)](../../role-based-access-control/role-assignments-portal.md) 指派可能會遭到移除。 當您帳戶所在的 Azure AD 租用戶不是訂用帳戶的租用戶，而且傳送移轉要求的使用者將訂用帳戶移至您帳戶的租用戶時，您就會失去存取權。 若要檢視具有[角色型存取 (RBAC) ](../../role-based-access-control/overview.md)存取權以管理訂用帳戶中資源的使用者，請使用下列步驟：

1. 瀏覽 [Azure 入口網站中的 [訂用帳戶] 頁面](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)。
1. 選取要查看的訂用帳戶，然後從左側窗格中選取 [存取控制 (IAM)]  。
1. 在頁面頂端選取 [角色指派]  。 [角色指派] 頁面會列出在訂用帳戶上具有 RBAC 存取權的所有使用者。

即使在轉移期間移除[角色型存取控制 (RBAC)](../../role-based-access-control/role-assignments-portal.md) 指派，原始擁有者帳戶中的使用者仍可透過一些安全性機制繼續存取訂用帳戶，包括：

* 可將使用者管理權限授與給訂用帳戶資源的管理憑證。 如需詳細資訊，請參閱[建立和上傳 Azure 的管理憑證](../../cloud-services/cloud-services-certs-create.md)。
* 服務 (例如儲存體) 的存取金鑰。 如需詳細資訊，請參閱[關於 Azure 儲存體帳戶](../../storage/common/storage-create-storage-account.md)。
* 服務 (例如 Azure 虛擬機器) 的遠端存取認證。

如果接受者需要限制對其資源的存取權，則應考慮更新所有與服務相關聯的密碼。 您可以使用下列步驟來更新大部分的資源：

  1. 登入 [Azure 入口網站](https://portal.azure.com)。
  2. 在 [中樞] 功能表中，選取 [所有資源]  。
  3. 選取資源。
  4. 在資源頁面中，選取 [設定]  。 您可以在這裡檢視並更新現有的密碼。

### <a name="if-i-take-over-the-billing-ownership-of-a-subscription-in-the-middle-of-the-billing-cycle-do-i-have-to-pay-for-the-entire-billing-cycle"></a>如果我在計費週期中接收訂用帳戶的帳單擁有權，我是否需要支付整個計費週期的費用？

您的帳戶負責支付針對轉移時間後所回報的任何使用量。 可能有某些使用量是在移轉之前發生，但在之後才報告。 使用量會包含在您帳務的帳單中。

### <a name="can-i-use-a-different-payment-method"></a>我可以使用不同的付款方式嗎？

是。 接受轉移要求時，您可以選取連結至您帳戶的現有付款方式，或新增付款方式。

### <a name="how-can-i-transfer-ownership-of-my-enterprise-agreement-ea-subscription-account-ownership-if-the-original-account-owner-is-no-longer-with-the-organization"></a>如果原始帳戶擁有者已離開組織，如何才能轉移 Enterprise 合約 (EA) 訂用帳戶擁有權？

即使原始帳戶擁有者不再屬於組織，Enterprise 系統管理員也可以更新任何帳戶的帳戶擁有權。 只要遵循在 EA 入口網站中[轉移所有訂用帳戶的帳戶擁有權](https://ea.azure.com/helpdocs/changeAccountOwnerForASubscription)指示，即可達成。

## <a name="troubleshooting"></a>疑難排解

### <a id="no-button"></a> 為什麼看不到 [移轉訂用帳戶] 按鈕？

您的計費帳戶無法使用自助式訂用帳戶轉移。 目前，我們不支援在 Azure 入口網站中，轉移 Enterprise 合約 (EA) 帳戶中的訂用帳戶帳單擁有權。 此外，與 Microsoft 代表共同建立的 Microsoft 客戶合約帳戶不支援轉移帳單擁有權。

### <a id="no-button"></a> 我的訂用帳戶類型為何不支援轉移？

並非所有的訂用帳戶類型都支援帳單擁有權轉移。 若要檢視支援轉移的訂用帳戶類型清單，請參閱[支援的訂用帳戶類型](#supported-subscription-types)

### <a id="no-button"></a> 當我嘗試轉移訂用帳戶的帳單擁有權時，我為何會收到拒絕存取錯誤？

如果您嘗試轉移 Microsoft Azure 方案訂用帳戶，但沒有必要的權限，您就會看到此錯誤。 若要轉移 Microsoft Azure 方案訂用帳戶，您在訂用帳戶計費的發票區段上必須是擁有者或參與者。 如需詳細資訊，請參閱[管理發票區段的訂用帳戶](understand-mca-roles.md#manage-subscriptions-for-invoice-section)。


## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡。

如有問題或需要協助，請[建立支援要求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>後續步驟

- 請檢閱並更新服務管理員、共同管理員和其他 RBAC 角色。 若要深入了解，請參閱[新增或變更 Azure 訂用帳戶管理員](add-change-subscription-administrator.md)和[使用 RBAC 和 Azure 入口網站來管理存取權](../../role-based-access-control/role-assignments-portal.md)。
