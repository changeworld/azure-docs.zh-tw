---
title: Azure AD 外部身分識別的 MAU 計費模型
description: 深入瞭解 Azure AD External 身分識別的每月作用中使用者 (MAU) 中來賓使用者共同作業 (B2B Azure AD 的) 計費模型。 瞭解如何將您的 Azure AD 租使用者連結至 Azure 訂用帳戶。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 09/21/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.workload: identity
ms.collection: M365-identity-device-management
ms.openlocfilehash: 983a803245467145a0b1161a4495e8045759e7ab
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2020
ms.locfileid: "92442060"
---
# <a name="billing-model-for-azure-ad-external-identities"></a>Azure AD 外部身分識別的計費模型

Azure Active Directory (Azure AD) 外部身分識別定價是根據每月作用中使用者 (MAU) ，也就是在日曆月份中具有驗證活動的唯一使用者計數。 此計費模型適用于 (B2B) 和 [Azure AD B2C](../../active-directory-b2c/billing.md)租使用者 Azure AD 來賓使用者共同作業。 MAU 計費可提供免費層和彈性且可預測的定價，協助您降低成本。 在本文中，您將瞭解如何 MAU 計費，以及將 Azure AD 租使用者連結至訂用帳戶。

> [!IMPORTANT]
> 本文不包含定價詳細資料。 如需使用方式計費和定價的最新資訊，請參閱 [Azure Active Directory 定價](https://azure.microsoft.com/pricing/details/active-directory/)。

## <a name="what-do-i-need-to-do"></a>我需要做些什麼？

若要利用 MAU 計費，您的 Azure AD 租使用者必須連結至 Azure 訂用帳戶。

|如果您的租使用者為：  |您必須：  |
|---------|---------|
| 已連結至訂用帳戶的 Azure AD 租使用者     | 不執行任何動作。 當您使用外部身分識別功能與來賓使用者共同作業時，將會使用 MAU 模型自動向您收費。        |
| 尚未連結至訂用帳戶的 Azure AD 租使用者     | 將[您的 Azure AD 租使用者連結至訂用](#link-your-azure-ad-tenant-to-a-subscription)帳戶，以啟用 MAU 計費。        |
|  |  |

## <a name="about-monthly-active-users-mau-billing"></a>關於每月活躍使用者 (MAU) 帳單

在您的 Azure AD 租使用者中，來賓使用者共同作業使用量的計費依據是在日曆月份中具有驗證活動的唯一來賓使用者計數。 此模型會以 1:5 的比例取代計費模型，在您的租用戶中，每個 Azure AD Premium 授權最多允許五位來賓使用者。 當租用戶結至訂用帳戶，而且您使用外部身分識別功能與來賓使用者共同作業時，將會使用以 MAU 為基礎的計費模型自動計費。
  
適用于來賓使用者的定價層是以指派給您 Azure AD 租使用者的最高定價層為基礎。 例如，如果您租使用者中的最高定價層是 Azure AD Premium P1，Premium P1 定價層也會套用至您的來賓使用者。 如果 Azure AD Free 最高的定價層，當您嘗試為來賓使用者使用 premium 功能時，系統會要求您升級至 premium 定價層。

## <a name="link-your-azure-ad-tenant-to-a-subscription"></a>將您的 Azure AD 租使用者連結至訂用帳戶

您必須將 Azure AD 租使用者連結至 Azure 訂用帳戶，才能適當地計費和存取功能。 如果目錄中還沒有您可以連結的訂用帳戶，您將有機會在此程式中新增一個。

1. 使用至少獲指派訂用帳戶中的[參與者](../../role-based-access-control/built-in-roles.md)角色或訂用帳戶內資源群組的 Azure 帳戶來登入[Azure 入口網站](https://portal.azure.com/)。

2. 選取您要連結的目錄：在 Azure 入口網站的工具列中，選取 [ **目錄 + 訂** 用帳戶] 圖示，然後選取目錄。

    ![選取 [目錄 + 訂用帳戶] 圖示](media/external-identities-pricing/portal-mau-pick-directory.png)

3. 在 [ **Azure 服務**] 下，選取 [ **Azure Active Directory**]。

4. 在左側功能表中，選取 [外部身分識別]。

5. 在 [ **訂閱**] 底下，選取 [ **連結的訂閱**]。

6. 在 [租使用者] 清單中，選取租使用者旁的核取方塊，然後選取 [ **連結訂**用帳戶]。

    ![選取租使用者並連結訂用帳戶](media/external-identities-pricing/linked-subscriptions.png)

7. 在 [連結訂用帳戶] 窗格中，選取 **訂** 用帳戶和 **資源群組**。 然後選取 [套用]。

   > [!NOTE]
   > 如果未列出任何訂用帳戶，您可以 [將訂用帳戶與您的租使用者建立關聯](../fundamentals/active-directory-how-subscriptions-associated-directory.md)。 或者， **如果您**還沒有訂用帳戶，您可以在這裡選取連結來新增訂用帳戶。

    ![選取訂用帳戶和資源群組](media/external-identities-pricing/link-subscription-resource.png)

完成這些步驟之後，您的 Azure 訂用帳戶會根據您的 Azure Direct 或 Enterprise 合約詳細資料（如果適用）計費。

## <a name="next-steps"></a>後續步驟

如需最新的定價資訊，請參閱 [Azure Active Directory 定價](https://azure.microsoft.com/pricing/details/active-directory/)。