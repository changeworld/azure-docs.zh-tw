---
title: Azure Lighthouse 案例中的租用戶、角色和使用者
description: 了解 Azure Active Directory 租用戶、使用者和角色的概念，以及如何在 Azure Lighthouse 案例中使用它們。
ms.date: 03/24/2020
ms.topic: conceptual
ms.openlocfilehash: 7540e17fd80f9a1d8e996295000c126614b838d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246886"
---
# <a name="tenants-roles-and-users-in-azure-lighthouse-scenarios"></a>Azure Lighthouse 案例中的租用戶、角色和使用者

讓客戶上線以進行 [Azure 委派的資源管理](azure-delegated-resource-management.md)之前，請務必了解 Azure Active Directory (Azure AD) 租用戶、使用者和角色如何運作，以及如何在 Azure Lighthouse 案例中使用它們。

*租用戶*是專用且受信任的 Azure AD 執行個體。 每個租用戶通常都代表一個組織。 Azure 委派的資源管理可讓使用者以邏輯方式將資源從某個租用戶投影至另一個租用戶。 如此可讓管理租用戶中的使用者 (例如，屬於服務提供者的使用者) 存取客戶租用戶中的委派資源，或讓[具有多個租用戶的企業集中管理營運](enterprise.md)。

為完成這個邏輯投影，客戶租用戶中的訂用帳戶 (或訂用帳戶內的一或多個資源群組) 必須是*上線*，才能進行 Azure 委派的資源管理。 此上線程序可以[透過 Azure Resource Manager 範本](../how-to/onboard-customer.md)，或藉由[將公用或私用供應項目發佈至 Azure Marketplace](../how-to/publish-managed-services-offers.md) 來完成。

無論您選擇哪一種上線方法，都必須定義*授權*。 每個授權都會在管理租用戶中指定一個可存取委派資源的使用者帳戶，以及一個可設定每個使用者對這些資源所擁有之權限的內建角色。

## <a name="role-support-for-azure-delegated-resource-management"></a>適用於 Azure 委派資源管理的角色支援

定義授權時，每個使用者帳戶都必須獲指派其中一個[角色型存取控制 (RBAC) 內建角色](../../role-based-access-control/built-in-roles.md)。 此外，不支援自訂角色與[傳統訂用帳戶管理員角色](../../role-based-access-control/classic-administrators.md) \(部分機器翻譯\)。

Azure 委派的資源管理目前支援所有[內建角色](../../role-based-access-control/built-in-roles.md)，但有下列例外狀況：

- 不支援[擁有者](../../role-based-access-control/built-in-roles.md#owner)角色。
- 不支援具有 [DataActions](../../role-based-access-control/role-definitions.md#dataactions) 權限的任何內建角色。
- 支援[使用者存取系統管理員](../../role-based-access-control/built-in-roles.md#user-access-administrator)內建角色，但僅限於[將角色指派給客戶租用戶中的受控識別](../how-to/deploy-policy-remediation.md#create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant)。 將不適用此角色通常授與的其他任何權限。 如果您定義具有此角色的使用者，您也必須指定此使用者可以指派給受控識別的內建角色。

> [!NOTE]
> 將適用的新內置角色添加到 Azure 後，可以使用[Azure 資源管理器範本在客戶入職](../how-to/onboard-customer.md)時為其分配該角色。 [發佈託管服務產品/](../how-to/publish-managed-services-offers.md)服務產品時，新添加的角色在雲合作夥伴門戶中可用之前可能會出現延遲。

## <a name="best-practices-for-defining-users-and-roles"></a>定義使用者和角色的最佳做法

建立授權時，建議您遵循下列最佳做法：

- 在多數情況下，建議您指派權限給 Azure AD 使用者群組或服務主體，而不是指派給一系列個別使用者帳戶。 如此一來，當您的存取需求變更時，就可以新增或移除個別使用者的存取權，而不需要更新並重新發佈方案。
- 請務必遵循最少特殊權限準則，讓使用者只具備完成其工作所需的權限，以協助減少發生意外錯誤的機會。 如需詳細資訊，請參閱[建議的安全性作法](../concepts/recommended-security-practices.md)。
- 包含具有[受控服務註冊指派刪除角色](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role)的使用者，讓您可以在稍後，視需要[移除對委派的存取權](../how-to/onboard-customer.md#remove-access-to-a-delegation)。 如果未指派此角色，則只有客戶租用戶中的使用者可以移除委派的資源。
- 請確定需要[在 Azure 入口網站檢視 [我的客戶] 頁面](../how-to/view-manage-customers.md)的任何使用者都具有[讀取者](../../role-based-access-control/built-in-roles.md#reader)角色 (或包含讀取者存取權的其他內建角色)。

> [!IMPORTANT]
> 為了添加 Azure AD 組的許可權，**組類型**必須是 **"安全"，** 而不是**Office 365**。 創建組時選擇此選項。 如需詳細資訊，請參閱[使用 Azure Active Directory 建立基本群組並新增成員](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)。

## <a name="next-steps"></a>後續步驟

- 了解 [Azure 委派資源管理的建議安全性做法](recommended-security-practices.md)。
- 將客戶上線至 Azure 委派的資源管理，方法是透過[使用 Azure Resource Manager 範本](../how-to/onboard-customer.md)或[將私人或公開受控服務供應項目發佈至 Azure Marketplace](../how-to/publish-managed-services-offers.md)。
