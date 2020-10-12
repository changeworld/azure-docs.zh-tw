---
title: Azure 委派的資源管理
description: Azure 委派的資源管理是 Azure Lighthouse 的關鍵區段，可讓服務提供者以靈活性和精確度大規模管理委派的資源。
ms.date: 08/12/2020
ms.topic: conceptual
ms.openlocfilehash: 9a499ceda546b7ea5c71cd8c770f1a4b99001b08
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88163521"
---
# <a name="azure-delegated-resource-management"></a>Azure 委派的資源管理

Azure 委派的資源管理是 [Azure Lighthouse](../overview.md)的重要元件之一。 透過 Azure 委派的資源管理，服務提供者可以簡化客戶業務開發及上線體驗，同時以彈性且精確的方式大規模地管理委派的資源。

## <a name="what-is-azure-delegated-resource-management"></a>什麼是 Azure 委派的資源管理？

Azure 委派的資源管理可讓使用者以邏輯方式將資源從某個租用戶投影至另一個。 這可讓某個 Azure Active Directory (Azure AD) 租用戶中的授權使用者，在屬於其客戶的不同 Azure AD 租用戶上執行管理作業。 服務提供者可以登入自己的 Azure AD 租用戶，並取得在委派的客戶訂用帳戶與資源群組中作業的授權。 這可讓他們代表其客戶執行管理作業，而不需要登入每個個別的客戶租用戶。

> [!TIP]
> Azure 委派的資源管理也可以用於[擁有多個 Azure AD 租用戶的企業內](enterprise.md)，以簡化跨租用戶管理。

透過 Azure 委派的資源管理，授權使用者可以直接在客戶訂用帳戶的內容中作業，而不需要在該客戶租用戶中擁有帳戶，或是成為該客戶租用戶的共同擁有者。

[跨租使用者管理體驗](cross-tenant-management-experience.md)可讓您更有效率地使用 Azure 管理服務（例如 Azure 原則、Azure 資訊安全中心等）來工作。 所有服務提供者活動都會在活動記錄檔中追蹤，此記錄會儲存在客戶的租使用者 (中，並可由管理租使用者) 中的使用者來查看。 這表示管理和受管理的租使用者中的使用者可以輕鬆地識別與任何變更相關聯的使用者。

您可以將 [新的受控服務供應專案類型發佈至 Azure Marketplace](../how-to/publish-managed-services-offers.md) ，讓客戶輕鬆地 Azure Lighthouse。 或者，您也可以[部署 Azure Resource Manager 範本來完成上線程序](../how-to/onboard-customer.md)。

## <a name="how-azure-delegated-resource-management-works"></a>Azure 委派的資源管理的運作方式

概括而言，以下是 Azure 委派的資源管理的運作方式：

1. 首先，您會識別 (角色的存取權，) 您的群組、服務主體或使用者將需要管理客戶的 Azure 資源。 存取定義包含管理租使用者識別碼，以及您租使用者中對應至[內建**roleDefinition**值](../../role-based-access-control/built-in-roles.md)的**principalId**身分識別 (參與者、VM 參與者、讀取者等 ) 。
2. 您可以使用下列兩種方式之一來指定此存取權，並讓客戶上線 Azure Lighthouse：
   - 將[Azure Marketplace 的受控服務供應專案發佈](../how-to/publish-managed-services-offers.md) (客戶將接受的私人或公用) 
   - 針對一或多個特定訂用帳戶或資源群組[將 Azure Resource Manager 範本部署至客戶的租用戶](../how-to/onboard-customer.md)
3. 一旦上線客戶之後，授權使用者就可以登入您的管理租使用者，並根據您所定義的存取權，在指定的客戶範圍執行工作。

> [!NOTE]
> 您可以管理位於不同 [區域](../../availability-zones/az-overview.md#regions)的委派資源。 但是，不支援跨 [全國雲端](../../active-directory/develop/authentication-national-cloud.md) 、Azure 公用雲端或跨兩個不同國家雲端的訂用帳戶委派。

## <a name="support-for-azure-delegated-resource-management"></a>適用於 Azure 委派的資源管理的支援

如果您需要與 Azure 委派的資源管理相關的協助，則可以在 Azure 入口網站中建立支援要求。 針對 [問題類型]****，請選擇 [技術]****。 選取訂用帳戶，然後選取 [**監視] & 管理**) 下的 [ **Lighthouse** (]。

## <a name="next-steps"></a>後續步驟

- 了解[跨租用戶管理體驗](cross-tenant-management-experience.md)。
- 了解 [Azure Marketplace 中的受控服務供應項目](managed-services-offers.md)。
