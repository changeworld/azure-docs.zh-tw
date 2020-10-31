---
title: 連結您的合作夥伴識別碼以追蹤您對委派資源的影響
description: 瞭解如何與您的合作夥伴識別碼建立關聯，以在您透過 Azure Lighthouse 管理的客戶資源上取得合作夥伴所獲得的點數 (PEC) 。
ms.date: 10/30/2020
ms.topic: how-to
ms.openlocfilehash: fcbcc70e380116b8e9f9b1c1e365dee1adb87a99
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93080272"
---
# <a name="link-your-partner-id-to-track-your-impact-on-delegated-resources"></a>連結您的合作夥伴識別碼以追蹤您對委派資源的影響 

如果您是 [Microsoft 合作夥伴網路](https://partner.microsoft.com/)的成員，您可以將合作夥伴識別碼連結至用來管理委派客戶資源的認證。 合作夥伴系統管理員連結 (PAL) 可讓 Microsoft 識別及辨識推動 Azure 客戶成功的合作夥伴。 此連結也可讓 [CSP (雲端解決方案提供者) ](/partner-center/csp-overview) 合作夥伴，以取得 [合作夥伴針對受控服務所獲得的點數 (PEC) ](/partner-center/partner-earned-credit) 已 [簽署 Microsoft 客戶合約 (MCA ](/partner-center/confirm-customer-agreement)) ，且位於 [Azure 方案下](/partner-center/azure-plan-get-started)。

如果您將 [Azure Marketplace 中的受控服務](publish-managed-services-offers.md)供應專案加入客戶，則會使用與發佈供應專案的合作夥伴中心帳戶相關聯的 MPN 識別碼，自動進行連結。 不需要採取進一步的動作，就能追蹤您對這些客戶的影響。

如果您 [使用 Azure 資源管理範本](onboard-customer.md)來讓客戶上線，您必須採取行動來建立此連結。 這是藉由將 [您的 MPN 識別碼](../../cost-management-billing/manage/link-partner-id.md) 與您管理租使用者中至少一個使用者帳戶連結，以存取您的每個上線訂用帳戶來完成。

## <a name="associate-your-partner-id-when-you-onboard-new-customers"></a>當您將新客戶上線時，請建立合作夥伴識別碼的關聯

當您透過 Azure Resource Manager 範本 (ARM 範本) 來將客戶上線時，請使用下列程式將您的合作夥伴識別碼連結 (，並啟用合作夥伴所獲得的點數（如果適用）) 。 您必須知道您的 [MPN 合作夥伴識別碼](/partner-center/partner-center-account-setup#locate-your-mpn-id) ，才能完成這些步驟。 請務必使用您的合作夥伴設定檔上顯示的 **相關聯 MPN 識別碼** 。

為了簡單起見，建議您在租使用者中建立服務主體帳戶，並將其連結至您的 **相關聯 MPN 識別碼** ，然後將其存取權授與您上架的每個客戶，並提供 [適合 PEC 的 Azure 內建角色](/partner-center/azure-roles-perms-pec)。

1. 在您的管理租使用者中[建立服務主體帳戶](../../active-directory/develop/howto-authenticate-service-principal-powershell.md)。 在此範例中，我們將使用此服務主體的名稱 *提供者自動化帳戶* 。
1. 使用該服務主體帳戶，連結至您管理租使用者中的 [相關聯 MPN 識別碼](../../cost-management-billing/manage/link-partner-id.md#link-to-a-partner-id) 。 您只需要進行一次。
1. 當您 [使用 ARM 範本讓客戶](onboard-customer.md)上線時，請務必包含包含提供者自動化帳戶的授權，其具有適用 [于 PEC 的 Azure 內建角色](/partner-center/azure-roles-perms-pec)的使用者。

藉由遵循這些步驟，您管理的每個客戶租使用者都會與您的合作夥伴識別碼相關聯。 提供者自動化帳戶不需要驗證或執行客戶租使用者中的任何動作。

:::image type="content" source="../media/lighthouse-pal.jpg" alt-text="顯示 PAL 處理 Azure Lighthouse 的圖表。":::

## <a name="add-your-partner-id-to-previously-onboarded-customers"></a>將您的合作夥伴識別碼新增至先前上線的客戶

如果您已上線客戶，您可能不想要執行其他部署來新增您的提供者自動化帳戶服務主體。 相反地，您可以將 **相關聯的 MPN 識別碼** 連結至已有權在該客戶的租使用者中工作的使用者帳戶。 請確定已將 [Azure 內建角色授與帳戶，且該角色符合 PEC 資格](/partner-center/azure-roles-perms-pec)。

一旦帳戶連結至您管理租使用者中的 [相關聯 MPN 識別碼](../../cost-management-billing/manage/link-partner-id.md#link-to-a-partner-id) 之後，您就可以追蹤對該客戶的影響。

## <a name="confirm-partner-earned-credit"></a>確認合作夥伴獲得的點數

您可以 [在 Azure 入口網站中查看 pec 詳細資料](/partner-center/partner-earned-credit-explanation#azure-cost-management) ，並確認哪些成本已獲得 pec 的優點。 請記住，PEC 僅適用于已簽署 MCA 且位於 Azure 方案下的 CSP 客戶。

如果您已遵循上述步驟，而且看不到預期的關聯，請在 Azure 入口網站中開啟支援要求。

您也可以使用 [合作夥伴中心 SDK](/partner-center/develop/get-invoice-unbilled-consumption-lineitems) 並進行篩選， `rateOfPartnerEarnedCredit` 將訂用帳戶的 PEC 驗證自動化。

## <a name="next-steps"></a>下一步

- 深入瞭解 [Microsoft 合作夥伴網路](/partner-center/mpn-overview)。
- 瞭解 [如何計算和支付 PEC](/partner-center/partner-earned-credit-explanation)。
- 讓客戶在 Azure Lighthouse 上[架](onboard-customer.md)。