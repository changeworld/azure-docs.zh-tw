---
title: 將受控服務供應專案發佈至 Azure Marketplace
description: 瞭解如何將將上線客戶的受控服務供應專案發佈至 Azure Lighthouse。
ms.date: 08/18/2020
ms.topic: how-to
ms.openlocfilehash: 11fb0073bcc3ba4437f78de8b263d165f0069db8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88589719"
---
# <a name="publish-a-managed-service-offer-to-azure-marketplace"></a>將受控服務供應專案發佈至 Azure Marketplace

在本文中，您將瞭解如何使用合作夥伴中心的[商業 Marketplace](../../marketplace/partner-center-portal/commercial-marketplace-overview.md)方案，將公用或私人受控服務供應專案發佈至[Azure Marketplace](https://azuremarketplace.microsoft.com) 。 購買供應專案的客戶會接著委派訂用帳戶或資源群組，讓您透過 [Azure Lighthouse](../overview.md)管理它們。

## <a name="publishing-requirements"></a>發佈需求

您必須 [在合作夥伴中心中](../../marketplace/partner-center-portal/create-account.md) 有有效的帳戶，才能建立和發佈供應專案。 如果您還沒有帳戶， [註冊](https://aka.ms/joinmarketplace) 程式會引導您完成在合作夥伴中心中建立帳戶，並在商業 Marketplace 方案中註冊的步驟。

根據 [受管理的服務供應專案認證需求](/legal/marketplace/certification-policies#7004-business-requirements)，您必須擁有 [銀級或金級雲端平臺專長認證等級](/partner-center/learn-about-competencies) 或 [Azure Expert MSP](https://partner.microsoft.com/membership/azure-expert-msp) ，才能發佈受控服務供應專案。

您的 Microsoft 合作夥伴網路 (MPN) 識別碼將會與您發佈的供應項目[自動關聯](../../cost-management-billing/manage/link-partner-id.md) \(部分機器翻譯\)，以追蹤您對客戶參與整體的影響。

如果您不想要將供應專案發佈至 Azure Marketplace，或不符合所有需求，您可以使用 Azure Resource Manager 範本，手動讓客戶上線。 如需詳細資訊，請參閱將 [客戶上架至 Azure Lighthouse](onboard-customer.md)。

下表可協助您藉由發佈受控服務供應專案或使用 Azure Resource Manager 範本，來決定是否要讓客戶上線。

|**考量**  |**受管理的服務供應專案**  |**ARM 範本**  |
|---------|---------|---------|
|需要 [合作夥伴中心帳戶](../../marketplace/partner-center-portal/create-account.md)   |是         |否        |
|需要 [銀級或金級雲端平臺專長認證等級](/partner-center/learn-about-competencies) 或 [Azure Expert MSP](https://partner.microsoft.com/membership/azure-expert-msp)      |是         |否         |
|可透過 Azure Marketplace 提供給新客戶     |是     |否       |
|可將供應專案限制為特定客戶     |是 (只有私人供應專案，無法與透過雲端解決方案提供者 (CSP) 計畫的轉銷商建立的訂用帳戶搭配使用)          |是         |
|需要 Azure 入口網站中接受客戶     |是     |否   |
|可以使用自動化來將多個訂用帳戶、資源群組或客戶上架 |否     |是    |
|立即存取新的內建角色和 Azure Lighthouse 功能     |在某些延遲之後，不一定會 (正式推出)          |是         |

## <a name="create-your-offer"></a>建立您的供應項目

如需有關如何建立供應專案的詳細指示，包括您需要提供的所有資訊和資產，請參閱 [建立受控服務供應](../../marketplace/partner-center-portal/create-new-managed-service-offer.md)專案。

若要瞭解一般發佈程式，請參閱 [Azure Marketplace 和 AppSource 發行指南](../../marketplace/marketplace-publishers-guide.md)。 您也應檢閱[商業市集認證原則](/legal/marketplace/certification-policies)，尤其是[受控服務](/legal/marketplace/certification-policies#700-managed-services)一節。

當客戶新增您的供應專案之後，他們將能夠委派一或多個訂用帳戶或資源群組，然後 [上線至 Azure Lighthouse](#the-customer-onboarding-process)。

> [!IMPORTANT]
> 受管理的服務供應專案中的每個方案都包含 **資訊清單詳細資料** 區段，您可以在其中定義租使用者中的 Azure Active Directory (Azure AD) 實體，以存取所購買方案的客戶所委派的資源群組和/或訂用帳戶。 請務必注意，您所包含的任何群組 (或使用者或服務主體) 都會具有與購買方案之每個客戶相同的許可權。 若要指派不同的群組以與每個客戶一起使用，您可以發行個別的 [私用方案](../../marketplace/private-offers.md) ，此方案對每個客戶都是專屬的。 請記住，透過雲端解決方案提供者 (CSP) 計畫的轉銷商所建立的訂用帳戶不支援私人方案。

## <a name="publish-your-offer"></a>發佈您的供應項目

完成所有區段之後，下一步是將供應項目發佈至 Azure Marketplace。 選取 [發行]**** 按鈕來起始供應項目上線程序。 如需此程式的詳細資訊，請參閱 [這裡](../../marketplace/partner-center-portal/create-new-managed-service-offer.md#publish)。 

您可以隨時[發佈供應項目的更新版本](../..//marketplace/partner-center-portal/update-existing-offer.md)。 例如，您可能會想要將新的角色定義新增至先前發佈的供應項目。 當您這麼做時，已新增供應項目的客戶會在 Azure 入口網站的 [[服務提供者](view-manage-service-providers.md)] 頁面中看到一個圖示，讓他們知道有可用的更新。 每個客戶都可以 [檢查變更](view-manage-service-providers.md#update-service-provider-offers) ，並決定是否要更新為新版本。 

## <a name="the-customer-onboarding-process"></a>客戶上線程序

當客戶新增您的供應專案之後，他們將能夠 [委派一或多個特定訂用帳戶或資源群組](view-manage-service-providers.md#delegate-resources)，然後上線至 Azure Lighthouse。 如果客戶已接受供應項目，但未委派任何資源，則他們會在 Azure 入口網站中[**服務提供者**](view-manage-service-providers.md)頁面上的 [提供者供應項目]**** 區段頂端看到一個備註。

> [!IMPORTANT]
> 委派必須由客戶租使用者中的非來賓帳戶完成，該帳戶具有 (上線之訂用帳戶的 [擁有者內建角色](../../role-based-access-control/built-in-roles.md#owner) ，或包含要上線) 的資源群組。 若要查看可委派訂用帳戶的所有使用者，客戶租用戶中的使用者可以在 Azure 入口網站中選取訂用帳戶並開啟 [存取控制 (IAM)]，然後[查看所有具有「擁有者」角色的使用者](../../role-based-access-control/role-assignments-list-portal.md#list-owners-of-a-subscription)。

一旦客戶將訂用帳戶委派 (或訂用帳戶) 內的一或多個資源群組，就會為該訂用帳戶註冊 **>microsoft.managedservices** 資源提供者，而且您租使用者中的使用者將能夠根據您供應專案中的授權存取委派的資源。

## <a name="next-steps"></a>接下來的步驟

- 瞭解 [商用 Marketplace](../../marketplace/partner-center-portal/commercial-marketplace-overview.md)。
- 了解[跨租用戶管理體驗](../concepts/cross-tenant-management-experience.md)。
- 前往 Azure 入口網站中的 [我的客戶]，以[檢視及管理客戶](view-manage-customers.md)。
