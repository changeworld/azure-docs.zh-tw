---
title: 關於移轉 Azure 訂用帳戶的帳單擁有權
description: 本文說明將 Azure 訂用帳戶的帳單擁有權轉移給另一個帳戶之前，您必須知道的事項。
keywords: 轉移 azure 訂用帳戶,azure 轉移訂用帳戶,將 azure 訂用帳戶移到另一個帳戶,azure 變更訂用帳戶擁有者,將 azure 訂用帳戶轉移給另一個帳戶, azure 轉移計費
author: bandersmsft
ms.reviewer: amberb
tags: billing,top-support-issue
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: banders
ms.custom: contperfq1
ms.openlocfilehash: 237565a7b72c3317e2c443f86965634ed7c9942c
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91336860"
---
# <a name="about-transferring-billing-ownership-for-an-azure-subscription"></a>關於移轉 Azure 訂用帳戶的帳單擁有權

本文會協助您了解將 Azure 訂用帳戶的帳單擁有權轉移給另一個帳戶之前，您應該知道的事項。 

如果您要離開組織，或想要向另一個帳戶收取訂用帳戶費用，您可以轉移 Azure 訂用帳戶的帳單擁有權。 將帳單擁有權轉移至另一個帳戶，可為新帳戶中的管理員提供計費工作的權限。 他們可以變更付款方式、查看費用，以及取消訂用帳戶。

如果您想要保留帳單擁有權，但變更訂用帳戶的類型，請參閱[將 Azure 訂用帳戶切換為其他供應項目](../manage/switch-azure-offer.md)。 若要控制誰可以存取訂用帳戶中的資源，請參閱 [Azure 內建角色](../../role-based-access-control/built-in-roles.md)。

如果您是 Enterprise 合約 (EA) 客戶，您的企業系統管理員可以在帳戶之間轉移訂用帳戶的帳單擁有權。

只有帳戶的計費管理員可以轉移訂用帳戶的擁有權。

## <a name="determine-account-billing-administrator"></a>判斷帳戶計費管理員

<a name="whoisaa"></a>

計費管理員是有權管理帳戶計費的人員。 他們已獲准存取 [Azure 入口網站](https://portal.azure.com)上的計費，並可執行各種計費工作，例如建立訂用帳戶、檢視和支付發票，或更新付款方式。

若要識別您是其計費管理員的帳戶，請造訪 [Azure 入口網站中的 [成本管理 + 帳單] 頁面](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/Overview)。 然後從左側窗格中選取 [所有計費範圍]。 [訂用帳戶] 頁面會顯示您在其中是計費管理員的所有訂用帳戶。

如果您不確定誰是訂用帳戶的帳戶管理員，請造訪 [Azure 入口網站中的 [訂用帳戶] 頁面](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)。 接著選取您要檢查的訂用帳戶，然後查看 [設定]。 選取 [屬性]，訂用帳戶的帳戶管理員會顯示在 [帳戶管理員] 方塊中。


## <a name="supported-subscription-types"></a>支援的訂用帳戶類型

Azure 入口網站中的訂用帳戶轉移適用於以下所列的訂用帳戶類型。 [免費試用](https://azure.microsoft.com/offers/ms-azr-0044p/) 或 [Azure in Open (AIO)](https://azure.microsoft.com/offers/ms-azr-0111p/) 訂用帳戶目前不支援轉移。 如需因應措施，請參閱[將資源移動到新的資源群組或訂用帳戶](../../azure-resource-manager/management/move-resource-group-and-subscription.md)。 若要轉移支援方案等其他訂用帳戶，請[連絡 Azure 支援](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

- [Enterprise 合約 (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)<sup>1</sup>
- [Microsoft 合作夥伴網路](https://azure.microsoft.com/offers/ms-azr-0025p/)  
- [Visual Studio Enterprise (MPN) 訂閱者](https://azure.microsoft.com/offers/ms-azr-0029p/)
- [MSDN 平台](https://azure.microsoft.com/offers/ms-azr-0062p/)  
- [隨用隨付](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [隨用隨付開發/測試](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/)
- [Visual Studio Enterprise：BizSpark](https://azure.microsoft.com/offers/ms-azr-0064p/)
- [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)
- [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/)
- [Microsoft Azure 方案](https://azure.microsoft.com/offers/ms-azr-0017g/)<sup>2</sup>

<sup>1</sup> 使用 EA 入口網站。

<sup>2</sup> 僅對在 Azure 網站註冊期間建立的帳戶提供支援。

## <a name="resources-transferred-with-subscriptions"></a>使用訂用帳戶轉移的資源

VM、磁碟和網站等所有資源都會轉移給新的帳戶。 不過，如果您將訂用帳戶轉移給另一個 Azure AD 租用戶中的帳戶，並不會轉移訂用帳戶上的任何[管理員角色](../manage/add-change-subscription-administrator.md)和 [Azure 角色指派](../../role-based-access-control/role-assignments-portal.md)。 此外，[應用程式註冊](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md)和其他租用戶特定服務也不會隨訂用帳戶轉移。

## <a name="transfer-account-ownership-to-another-countryregion"></a>將帳戶擁有權轉移至另一個國家/區域

很遺憾，您無法使用 Azure 入口網站，在國家/地區或區域間轉移訂用帳戶。 不過，如果您開啟 Azure 支援要求，就可以轉移帳戶。 若要建立支援要求，請[連絡支援](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

## <a name="transfer-a-subscription-from-one-account-to-another"></a>將訂用帳戶從一個帳戶轉移給另一個帳戶

如果您是兩個帳戶的系統管理員，您可以在您的帳戶之間轉移訂用帳戶。 您的帳戶在概念上會視為兩個不同使用者的帳戶，因此您可以在您的帳戶之間轉移訂用帳戶。
若要檢視轉移訂用帳戶的必要步驟，請參閱[轉移 Azure 訂用帳戶的帳單擁有權](../manage/billing-subscription-transfer.md)。

## <a name="transferring-a-subscription-shouldnt-create-downtime"></a>轉移訂用帳戶不應產生停機時間

如果您將訂用帳戶轉移至相同 Azure Active Directory 租用戶中的帳戶，並不會影響在訂用帳戶中執行的資源。 不過，儲存在 PowerShell 中的內容資訊並不會更新，因此您可能必須加以清除或變更設定。 如果您將訂用帳戶轉移到另一個租用戶中的帳戶，並決定將訂用帳戶移至該租用戶，則以 [Azure 角色指派](../../role-based-access-control/role-assignments-portal.md)來管理訂用帳戶中資源的所有使用者、群組和服務主體都會失去其存取權。 這可能會造成服務停機。

## <a name="new-account-usage-and-billing-history"></a>新帳戶使用量及帳單記錄

新帳戶使用者唯一可用的資訊是您訂用帳戶的上個月成本。 其餘的使用量及帳單記錄不會隨著訂用帳戶一起移轉。

## <a name="manually-migrate-data-and-services"></a>手動遷移資料和服務

當您轉移訂用帳戶時，其資源會保持不變。 如果您無法轉移訂用帳戶擁有權，則可手動遷移其資源。 如需詳細資訊，請參閱 [將資源移動到新的資源群組或訂用帳戶](../../azure-resource-manager/management/move-resource-group-and-subscription.md)。

## <a name="remaining-subscription-credits"></a>剩餘訂用帳戶點數 

如果您有 Visual Studio 或 Microsoft 合作夥伴網路訂用帳戶，您可以取得每月點數。 您的點數不會與新帳戶中的訂用帳戶一起轉移。 接受傳輸要求的使用者必須具有 Visual Studio 授權，才能接受轉移要求。 訂用帳戶會使用使用者帳戶中可用的 Visual Studio 點數。 如需詳細資訊，請參閱[轉移 Visual Studio 和合作夥伴網路訂用帳戶](../manage/billing-subscription-transfer.md#transfer-visual-studio-and-partner-network-subscriptions)。

## <a name="users-keep-access-to-transferred-resources"></a>使用者可繼續存取已轉移的資源

請記住，擁有訂用帳戶中資源存取權的使用者，會在轉移擁有權之後保有其存取權。 不過，[管理員角色](../manage/add-change-subscription-administrator.md)和 [Azure 角色指派](../../role-based-access-control/role-assignments-portal.md)可能會遭到移除。 當您帳戶所在的 Azure AD 租用戶不是訂用帳戶的租用戶，而且傳送移轉要求的使用者將訂用帳戶移至您帳戶的租用戶時，您就會失去存取權。 

您可以檢視使用者，這些使用者具有 Azure 角色指派，可以存取 Azure 入口網站訂用帳戶中的資源。 瀏覽 [Azure 入口網站中的 [訂用帳戶] 頁面](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)。 然後選取要查看的訂用帳戶，然後從左側窗格中選取 [存取控制 (IAM)]。 接下來，在頁面頂端選取 [角色指派]。 [角色指派] 頁面會列出在訂用帳戶上具有存取權的所有使用者。

即使在轉移期間移除 [Azure 角色指派](../../role-based-access-control/role-assignments-portal.md)，原始擁有者帳戶中的使用者仍可透過其他安全性機制繼續存取訂用帳戶，包括：

* 可將使用者管理權限授與給訂用帳戶資源的管理憑證。 如需詳細資訊，請參閱[建立和上傳 Azure 的管理憑證](../../cloud-services/cloud-services-certs-create.md)。
* 服務 (例如儲存體) 的存取金鑰。 如需詳細資訊，請參閱[關於 Azure 儲存體帳戶](../../storage/common/storage-create-storage-account.md)。
* 服務 (例如 Azure 虛擬機器) 的遠端存取認證。

如果接受者需要限制對資源的存取權，則應考慮更新所有與服務相關聯的密碼。 大部分資源都可以更新。 登入 [Azure 入口網站](https://portal.azure.com)，然後在 [中樞] 功能表中選取 [所有資源]。 接下來選取資源。 然後在資源頁面中，選取 [設定]。 您可以在該處檢視並更新現有的密碼。

## <a name="you-pay-for-usage-when-you-receive-ownership"></a>當您收到擁有權時，您需支付使用量

您的帳戶負責支付針對轉移時間後所回報的任何使用量。 可能有某些使用量是在移轉之前發生，但在之後才報告。 使用量會包含在您帳務的帳單中。

## <a name="use-a-different-payment-method"></a>使用不同的付款方式

接受轉移要求時，您可以選取連結至您帳戶的現有付款方式，或新增付款方式。

## <a name="transfer-enterprise-agreement-subscription-ownership"></a>轉移 Enterprise 合約訂用帳戶擁有權

即使原始帳戶擁有者不再屬於組織，Enterprise 系統管理員也可以更新任何帳戶的帳戶擁有權。 如需有關轉移 Azure Enterprise 合約帳戶的詳細資訊，請參閱 [Azure 企業版轉移](../manage/ea-transfers.md)。

## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡。

如果您有問題或需要協助，請[建立支援要求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>後續步驟

- [轉移 Azure 訂用帳戶的帳單擁有權](../manage/billing-subscription-transfer.md)
