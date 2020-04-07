---
title: 向 Azure 應用商店發佈託管服務產品/服務
description: 了解如何發佈將客戶上線至 Azure 委派資源管理的受控服務。
ms.date: 03/30/2020
ms.topic: conceptual
ms.openlocfilehash: 922e78a0715ce5c758ca7d068a38af43e214a524
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673940"
---
# <a name="publish-a-managed-service-offer-to-azure-marketplace"></a>向 Azure 應用商店發佈託管服務產品/服務

在本文中,您將學習如何使用[合作夥伴中心](https://partner.microsoft.com/)中的[商業應用商店](../../marketplace/partner-center-portal/commercial-marketplace-overview.md)計劃向[Azure 應用商店](https://azuremarketplace.microsoft.com)發佈公共或私有託管服務產品/ 然後,購買產品/服務的客戶將能夠將訂閱和資源組用於[Azure 委派的資源管理](../concepts/azure-delegated-resource-management.md)。

## <a name="publishing-requirements"></a>發佈需求

您需要[在合作夥伴中心](../../marketplace/partner-center-portal/create-account.md)中擁有有效的帳戶才能創建和發佈產品/服務。 如果您還沒有帳戶,[註冊過程](https://aka.ms/joinmarketplace)將引導您完成在合作夥伴中心創建帳戶並註冊商業市場計劃的步驟。

根據[託管服務提供認證要求](https://docs.microsoft.com/legal/marketplace/certification-policies#7004-business-requirements),您必須具有[銀雲或金雲平臺能力級別](https://docs.microsoft.com/partner-center/learn-about-competencies)或 Azure 專家[MSP](https://partner.microsoft.com/membership/azure-expert-msp)才能發佈託管服務產品/服務。

您的 Microsoft 合作夥伴網路 (MPN) 識別碼將會與您發佈的供應項目[自動關聯](../../billing/billing-partner-admin-link-started.md) \(部分機器翻譯\)，以追蹤您對客戶參與整體的影響。

> [!NOTE]
> 如果您不想將供應項目發佈至 Azure Marketplace，您可以使用 Azure Resource Manager 範本手動讓客戶上線。 如需詳細資訊，請參閱[讓客戶在 Azure 委派的資源管理中上線](onboard-customer.md)。

## <a name="create-your-offer"></a>建立您的供應項目

有關如何建立產品/服務的詳細資訊(包括您需要提供的所有資訊和資產)的詳細說明,請參閱[創建新的託管服務產品/服務](../../marketplace/partner-center-portal/create-new-managed-service-offer.md)。

要瞭解常規發佈過程,請參閱[Azure 應用商店和 AppSource 發表指南](../../marketplace/marketplace-publishers-guide.md)。 您也應檢閱[商業市集認證原則](https://docs.microsoft.com/legal/marketplace/certification-policies)，尤其是[受控服務](https://docs.microsoft.com/legal/marketplace/certification-policies#700-managed-services)一節。

客戶新增產品/服務後,他們將能夠委派一個或多個訂閱或資源組,然後這些訂閱或資源組將[註冊用於 Azure 委派的資源管理](#the-customer-onboarding-process)。

> [!IMPORTANT]
> Each plan in a Managed service offer includes a **Manifest Details** section, where you define the Azure Active Directory (Azure AD) entities in your tenant that will have access to the delegated resource groups and/or subscriptions for customers who purchase that plan. 請務必注意,您包括的任何組(或使用者或服務主體)對於購買計劃的每個客戶都將具有相同的許可權。 要配置不同的群組以與每個客戶合作,您需要發表每個客戶獨有的單獨[私人計畫](../../marketplace/private-offers.md)。

## <a name="publish-your-offer"></a>發佈您的供應項目

完成所有區段之後，下一步是將供應項目發佈至 Azure Marketplace。 選取 [發行]**** 按鈕來起始供應項目上線程序。 有關此過程的詳細資訊,請參閱[此處](../../marketplace/partner-center-portal/create-new-managed-service-offer.md#publish)。

您可以隨時[發佈供應項目的更新版本](../../marketplace/partner-center-portal/update-existing-offer.md)。 例如，您可能會想要將新的角色定義新增至先前發佈的供應項目。 當您這麼做時，已新增供應項目的客戶會在 Azure 入口網站的 [[服務提供者****](view-manage-service-providers.md)] 頁面中看到一個圖示，讓他們知道有可用的更新。 每個客戶都可以[檢閱變更](view-manage-service-providers.md#update-service-provider-offers)，並決定是否要更新為新的版本。 

## <a name="the-customer-onboarding-process"></a>客戶上線程序

客戶新增您的供應項目之後，他們將能夠[委派一個或多個特定訂用帳戶或資源群組](view-manage-service-providers.md#delegate-resources)，然後讓這些項目上線，以進行 Azure 委派的資源管理。 如果客戶已接受供應項目，但未委派任何資源，則他們會在 Azure 入口網站中[**服務提供者**](view-manage-service-providers.md)頁面上的 [提供者供應項目]**** 區段頂端看到一個備註。

> [!IMPORTANT]
> 委派必須由客戶租戶中的非來賓帳戶完成,該帳戶具有已加入的訂閱[的所有者內置角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner)(或包含正在裝機的資源組)。 若要查看可委派訂用帳戶的所有使用者，客戶租用戶中的使用者可以在 Azure 入口網站中選取訂用帳戶並開啟 [存取控制 (IAM)]****，然後[查看所有具有「擁有者」角色的使用者](../../role-based-access-control/role-assignments-list-portal.md#list-owners-of-a-subscription)。

一旦客戶委派訂閱(或訂閱中的一個或多個資源組 **),Microsoft.託管服務**資源提供程式將註冊該訂閱,租戶中的使用者將能夠根據產品/服務中的授權訪問委派的資源。

## <a name="next-steps"></a>後續步驟

- 瞭解[商業市場](../../marketplace/partner-center-portal/commercial-marketplace-overview.md)。
- 了解[跨租用戶管理體驗](../concepts/cross-tenant-management-experience.md)。
- 前往 Azure 入口網站中的 [我的客戶]****，以[檢視及管理客戶](view-manage-customers.md)。
