---
title: 將受控服務供應專案發佈至 Azure Marketplace
description: 瞭解如何將將上線客戶的受控服務供應專案發佈至 Azure 燈塔。
ms.date: 07/28/2020
ms.topic: how-to
ms.openlocfilehash: e86cec16cc377470f255c8b677d7462dee6b3573
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2020
ms.locfileid: "87407059"
---
# <a name="publish-a-managed-service-offer-to-azure-marketplace"></a>將受控服務供應專案發佈至 Azure Marketplace

在本文中，您將瞭解如何使用合作夥伴中心的[商業 Marketplace](../../marketplace/partner-center-portal/commercial-marketplace-overview.md)方案，將公開或私人受控服務供應專案發佈至[Azure Marketplace](https://azuremarketplace.microsoft.com) 。 購買供應專案的客戶會接著委派訂用帳戶或資源群組，讓您可以透過[Azure 燈塔](../overview.md)來管理這些訂用帳戶。

## <a name="publishing-requirements"></a>發佈需求

您必須[在合作夥伴中心](../../marketplace/partner-center-portal/create-account.md)擁有有效的帳戶，才能建立及發佈供應專案。 如果您還沒有帳戶，[註冊](https://aka.ms/joinmarketplace)程式會引導您完成在合作夥伴中心建立帳戶，並在商業 Marketplace 方案中註冊的步驟。

根據[受控服務提供認證需求](/legal/marketplace/certification-policies#7004-business-requirements)，您必須擁有[銀級或金級雲端平臺專長認證等級](/partner-center/learn-about-competencies)，或為[Azure 專家的 MSP](https://partner.microsoft.com/membership/azure-expert-msp) ，才能發佈受控服務供應專案。

您的 Microsoft 合作夥伴網路 (MPN) 識別碼將會與您發佈的供應項目[自動關聯](../../cost-management-billing/manage/link-partner-id.md) \(部分機器翻譯\)，以追蹤您對客戶參與整體的影響。

> [!NOTE]
> 如果您不想將供應項目發佈至 Azure Marketplace，您可以使用 Azure Resource Manager 範本手動讓客戶上線。 如需詳細資訊，請參閱[將客戶上架至 Azure 燈塔](onboard-customer.md)。

## <a name="create-your-offer"></a>建立您的供應項目

如需如何建立供應專案的詳細指示，包括您必須提供的所有資訊和資產，請參閱[建立受控服務供應](../../marketplace/partner-center-portal/create-new-managed-service-offer.md)專案。

若要瞭解一般發佈程式，請參閱[Azure Marketplace 和 AppSource 發佈指南](../../marketplace/marketplace-publishers-guide.md)。 您也應檢閱[商業市集認證原則](/legal/marketplace/certification-policies)，尤其是[受控服務](/legal/marketplace/certification-policies#700-managed-services)一節。

一旦客戶加入您的供應專案，他們就能夠委派一或多個訂用帳戶或資源群組，然後再將其[上架至 Azure 燈塔](#the-customer-onboarding-process)。

> [!IMPORTANT]
> 受控服務供應專案中的每個方案都包含 [**資訊清單詳細資料**] 區段，您可以在其中定義租使用者中的 Azure Active Directory （Azure AD）實體，以存取購買該方案之客戶的委派資源群組和/或訂用帳戶。 請務必注意，您所包含的任何群組（或使用者或服務主體）對於每個購買方案的客戶都有相同的許可權。 若要指派不同的群組來與每個客戶一起使用，您可以發行個別的[私用方案](../../marketplace/private-offers.md)，以供每個客戶使用。 請記住，透過雲端解決方案提供者（CSP）計畫的轉銷商所建立的訂用帳戶不支援私用方案。

## <a name="publish-your-offer"></a>發佈您的供應項目

完成所有區段之後，下一步是將供應項目發佈至 Azure Marketplace。 選取 [發行]**** 按鈕來起始供應項目上線程序。 如需此程式的詳細資訊，請參閱[這裡](../../marketplace/partner-center-portal/create-new-managed-service-offer.md#publish)。 

您可以隨時[發佈供應項目的更新版本](../..//marketplace/partner-center-portal/update-existing-offer.md)。 例如，您可能會想要將新的角色定義新增至先前發佈的供應項目。 當您這麼做時，已新增供應項目的客戶會在 Azure 入口網站的 [[服務提供者](view-manage-service-providers.md)] 頁面中看到一個圖示，讓他們知道有可用的更新。 每位客戶都可以[檢查變更](view-manage-service-providers.md#update-service-provider-offers)，並決定是否要更新為新的版本。 

## <a name="the-customer-onboarding-process"></a>客戶上線程序

客戶新增您的供應專案之後，他們將能夠[委派一或多個特定](view-manage-service-providers.md#delegate-resources)的訂用帳戶或資源群組，然後再將其上架至 Azure 燈塔。 如果客戶已接受供應項目，但未委派任何資源，則他們會在 Azure 入口網站中[**服務提供者**](view-manage-service-providers.md)頁面上的 [提供者供應項目]**** 區段頂端看到一個備註。

> [!IMPORTANT]
> 委派必須由客戶租使用者中的非來賓帳戶完成，其中具有所要上架之訂用帳戶的[擁有者內建角色](../../role-based-access-control/built-in-roles.md#owner)（或包含正在上架的資源群組）。 若要查看可委派訂用帳戶的所有使用者，客戶租用戶中的使用者可以在 Azure 入口網站中選取訂用帳戶並開啟 [存取控制 (IAM)]，然後[查看所有具有「擁有者」角色的使用者](../../role-based-access-control/role-assignments-list-portal.md#list-owners-of-a-subscription)。

一旦客戶委派了訂用帳戶（或訂用帳戶內的一或多個資源群組）， **ManagedServices**資源提供者就會註冊該訂用帳戶，而您租使用者中的使用者將能夠根據您供應專案中的授權存取委派的資源。

## <a name="next-steps"></a>後續步驟

- 瞭解[商業 Marketplace](../../marketplace/partner-center-portal/commercial-marketplace-overview.md)。
- 了解[跨租用戶管理體驗](../concepts/cross-tenant-management-experience.md)。
- 前往 Azure 入口網站中的 [我的客戶]，以[檢視及管理客戶](view-manage-customers.md)。
