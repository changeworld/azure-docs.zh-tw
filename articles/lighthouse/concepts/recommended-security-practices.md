---
title: 建議的安全性作法
description: 使用 Azure 燈塔時，請務必考慮安全性和存取控制。
ms.date: 08/12/2020
ms.topic: conceptual
ms.openlocfilehash: ddc4d2c69e7aacc49c5309bc407aecd9bc8e572f
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/13/2020
ms.locfileid: "88163266"
---
# <a name="recommended-security-practices"></a>建議的安全性作法

使用[Azure 燈塔](../overview.md)時，請務必考慮安全性和存取控制。 您租使用者中的使用者將可直接存取客戶訂用帳戶和資源群組，因此您會想要採取步驟來維護租使用者的安全性。 您也會想要確保只允許有效管理客戶資源所需的存取權。 此主題會提供可協助您這樣做的建議。

> [!TIP]
> 這些建議也適用于使用 Azure 燈塔[管理多個](enterprise.md)租使用者的企業。

## <a name="require-azure-multi-factor-authentication"></a>要求使用 Azure Multi-Factor Authentication

[Azure 多重要素驗證](../../active-directory/authentication/concept-mfa-howitworks.md) (也稱為雙步驟驗證) 有助於防止攻擊者藉由要求多個驗證步驟來取得帳戶的存取權。 您應該針對管理租使用者中的所有使用者要求多重要素驗證，包括能夠存取委派客戶資源的使用者。

我們建議您要求客戶也在其租用戶中實作 Azure Multi-Factor Authentication。

## <a name="assign-permissions-to-groups-using-the-principle-of-least-privilege"></a>使用最低權限原則將權限指派給群組

若要簡化管理，請針對管理客戶資源所需的每個角色，使用 Azure Active Directory (Azure AD) 群組。 這可讓您視需要在群組中新增或移除個別使用者，而不是直接將許可權指派給每個使用者。

> [!IMPORTANT]
> 若要新增 Azure AD 群組的許可權，[群組類型] 必須是 [安全性]，而不是 [Office 365]。 建立群組時，會選取此選項。 如需詳細資訊，請參閱[使用 Azure Active Directory 建立基本群組並新增成員](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)。

建立權限結構時，請務必遵循最低權限原則來讓使用者只具備完成其工作所需的權限，以協助減少發生意外錯誤的機會。

例如，您應該使用像這樣的結構：

|群組名稱  |類型  |principalId  |角色定義  |角色定義識別碼  |
|---------|---------|---------|---------|---------|
|結構設計師     |使用者群組         |\<principalId\>         |參與者         |b24988ac-6180-42a0-ab88-20f7382dd24c  |
|評量     |使用者群組         |\<principalId\>         |讀者         |acdd72a7-3385-48ef-bd42-f606fba81ae7  |
|VM 專家     |使用者群組         |\<principalId\>         |VM 參與者         |9980e02c-c2be-4d73-94e8-173b1dc7cf3c  |
|自動化     |服務主體名稱 (SPN)         |\<principalId\>         |參與者         |b24988ac-6180-42a0-ab88-20f7382dd24c  |

建立這些群組之後，您可以視需要指派使用者。 請僅加入真正需要存取權的使用者。 請務必定期檢閱群組成員，並移除已不再適合或需要包含在該群組內的任何使用者。

請注意，當您[透過公用受控服務供應項目將客戶上線](../how-to/publish-managed-services-offers.md)時，您包含的任何群組 (或使用者或服務主體) 都將具有和購買該方案的每個客戶相同的權限。 若要指派不同的群組來與每個客戶一起使用，您必須發行個別的私用方案，這是每個客戶專屬，或使用 Azure Resource Manager 範本來個別讓客戶上線。 例如，您可以發佈存取非常有限的公用方案，然後使用自訂的 Azure Resource 範本來視需要授與額外的存取權，並直接與客戶合作以將其資源上線來取得額外的存取權。

## <a name="next-steps"></a>後續步驟

- [部署 Azure 多重要素驗證](../../active-directory/authentication/howto-mfa-getstarted.md)。
- 了解[跨租用戶管理體驗](cross-tenant-management-experience.md)。
