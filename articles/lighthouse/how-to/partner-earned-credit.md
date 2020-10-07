---
title: 連結您的合作夥伴識別碼，以在委派的資源上啟用合作夥伴所獲得的點數
description: 瞭解如何與您的合作夥伴識別碼建立關聯，以在您透過 Azure Lighthouse 管理的客戶資源上取得合作夥伴所獲得的點數 (PEC) 。
ms.date: 10/05/2020
ms.topic: how-to
ms.openlocfilehash: 5caa205ce74152c7ec047952f66c1bf9188ddf02
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/06/2020
ms.locfileid: "91776165"
---
# <a name="link-your-partner-id-to-enable-partner-earned-credit-on-delegated-resources"></a>連結您的合作夥伴識別碼，以在委派的資源上啟用合作夥伴所獲得的點數

如果您是 [Microsoft 合作夥伴網路](https://partner.microsoft.com/)的成員，您可以將合作夥伴識別碼連結至用來管理委派客戶資源的認證。 這可讓您追蹤客戶參與情況的影響，並獲得 [ (PEC) 受控服務的合作夥伴 ](/partner-center/partner-earned-credit)所獲得的點數。

如果您將 [Azure Marketplace 中的受控服務提供給客戶](publish-managed-services-offers.md)，此連結會自動執行，並使用與用來發佈供應專案的合作夥伴中心帳戶相關聯的 MPN 識別碼。 不需要採取進一步的動作，就能接收這些客戶的 PEC。

如果您 [使用 Azure 資源管理範本](onboard-customer.md)來讓客戶上線，您必須採取行動來建立此連結。 這是藉由將 [您的 MPN 識別碼](../../cost-management-billing/manage/link-partner-id.md) 與您管理租使用者中至少一個使用者帳戶連結，以存取您的每個上線訂用帳戶來完成。

## <a name="associate-your-partner-id-when-you-onboard-new-customers"></a>當您將新客戶上線時，請建立合作夥伴識別碼的關聯

當您透過 Azure Resource Manager 範本將客戶上架 (ARM 範本) 時，請使用下列程式連結您的合作夥伴識別碼，並讓合作夥伴獲得點數。 您必須知道您的 [MPN 合作夥伴識別碼](/partner-center/partner-center-account-setup#locate-your-mpn-id) ，才能完成這些步驟。 請務必使用您夥伴設定檔上所顯示的 **相關聯 MPN 識別碼** 。

為了簡單起見，建議您在租使用者中建立服務主體帳戶，並將其連結至您的 **相關聯 MPN 識別碼**，然後將其存取權授與您上架的每個客戶，並提供 [適合 PEC 的 Azure 內建角色](https://docs.microsoft.com/partner-center/azure-roles-perms-pec)。

1. 在您的管理租使用者中[建立服務主體帳戶](../../active-directory/develop/howto-authenticate-service-principal-powershell.md)。 在此範例中，我們會將此服務主體命名為 PEC 自動化帳戶。
1. 使用該服務主體帳戶，連結至您管理租使用者中的 [相關聯 MPN 識別碼](../../cost-management-billing/manage/link-partner-id.md#link-to-a-partner-id) 。 您只需要進行一次。
1. 當您 [使用 ARM 範本讓客戶](onboard-customer.md)上線時，請務必包含包含 Pec 自動化帳戶的授權，並以具有適用 [于 pec 之 Azure 內建角色](https://docs.microsoft.com/partner-center/azure-roles-perms-pec)的使用者來提供。

藉由遵循這些步驟，您所管理的每個客戶租使用者都會與您的合作夥伴識別碼相關聯，讓您能夠接收這些客戶的 PEC。 PEC 自動化帳戶不需要驗證或執行客戶租使用者中的任何動作。

## <a name="add-your-partner-id-to-previously-onboarded-customers"></a>將您的合作夥伴識別碼新增至先前上線的客戶

如果您已上線客戶，您可能不想要執行其他部署來新增您的 PEC 自動化帳戶服務主體。 相反地，您可以將 **相關聯的 MPN 識別碼** 連結至已有權在該客戶的租使用者中工作的使用者帳戶。 請確定已將 [Azure 內建角色授與帳戶，且該角色符合 PEC 資格](https://docs.microsoft.com/partner-center/azure-roles-perms-pec)。

一旦帳戶連結至您管理租使用者中的 [相關聯 MPN 識別碼](../../cost-management-billing/manage/link-partner-id.md#link-to-a-partner-id) 之後，您就可以接收該客戶的 PEC。

## <a name="confirm-partner-earned-credit"></a>確認合作夥伴獲得的點數

您可以 [在 Azure 入口網站中查看 pec 詳細資料](/partner-center/partner-earned-credit-explanation#azure-cost-management) ，並確認哪些成本已獲得 pec 的優點。

如果您已遵循上述步驟，而且沒有看到關聯，請在 Azure 入口網站中開啟支援要求。

## <a name="next-steps"></a>後續步驟

- 深入瞭解 [Microsoft 合作夥伴網路](/partner-center/mpn-overview)。
- 瞭解 [如何計算和支付 PEC](/partner-center/partner-earned-credit-explanation)。
- 讓客戶在 Azure Lighthouse 上[架](onboard-customer.md)。
