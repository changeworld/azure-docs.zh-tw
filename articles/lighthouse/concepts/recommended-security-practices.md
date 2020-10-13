---
title: 建議的安全性作法
description: 使用 Azure Lighthouse 時，請務必考慮安全性和存取控制。
ms.date: 08/12/2020
ms.topic: conceptual
ms.openlocfilehash: 097611837e3bba2d1a44ec521862e03cc467d707
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91399375"
---
# <a name="recommended-security-practices"></a>建議的安全性作法

使用 [Azure Lighthouse](../overview.md)時，請務必考慮安全性和存取控制。 您租使用者中的使用者可以直接存取客戶訂用帳戶和資源群組，因此您會想要採取步驟來維護租使用者的安全性。 您也會想要確保只允許有效管理客戶資源所需的存取權。 此主題會提供可協助您這樣做的建議。

> [!TIP]
> 這些建議也適用于使用 Azure Lighthouse [管理多個](enterprise.md) 租使用者的企業。

## <a name="require-azure-multi-factor-authentication"></a>要求使用 Azure Multi-Factor Authentication

[Azure Multi-Factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md) (也稱為雙步驟驗證) 有助於防止攻擊者藉由要求多個驗證步驟來取得帳戶的存取權。 您應要求管理租使用者中的所有使用者 Multi-Factor Authentication，包括可存取委派客戶資源的使用者。

我們建議您要求客戶也在其租用戶中實作 Azure Multi-Factor Authentication。

## <a name="assign-permissions-to-groups-using-the-principle-of-least-privilege"></a>使用最低權限原則將權限指派給群組

若要讓管理更容易，請使用 Azure Active Directory (Azure AD 管理客戶資源所需的每個角色) 群組。 這可讓您視需要在群組中新增或移除個別使用者，而不是將許可權直接指派給每位使用者。

> [!IMPORTANT]
> 為了新增 Azure AD 群組的許可權， **群組類型** 必須設定為 [ **安全性**]。 建立群組時，會選取此選項。 如需詳細資訊，請參閱[使用 Azure Active Directory 建立基本群組並新增成員](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)。

建立權限結構時，請務必遵循最低權限原則來讓使用者只具備完成其工作所需的權限，以協助減少發生意外錯誤的機會。

例如，您應該使用像這樣的結構：

|群組名稱  |類型  |principalId  |角色定義  |角色定義識別碼  |
|---------|---------|---------|---------|---------|
|結構設計師     |使用者群組         |\<principalId\>         |參與者         |b24988ac-6180-42a0-ab88-20f7382dd24c  |
|評量     |使用者群組         |\<principalId\>         |讀者         |acdd72a7-3385-48ef-bd42-f606fba81ae7  |
|VM 專家     |使用者群組         |\<principalId\>         |VM 參與者         |9980e02c-c2be-4d73-94e8-173b1dc7cf3c  |
|自動化     |服務主體名稱 (SPN)         |\<principalId\>         |參與者         |b24988ac-6180-42a0-ab88-20f7382dd24c  |

建立這些群組之後，您就可以視需要指派使用者。 請僅加入真正需要存取權的使用者。 請務必定期檢閱群組成員，並移除已不再適合或需要包含在該群組內的任何使用者。

請注意，當您[透過公用受控服務供應項目將客戶上線](../how-to/publish-managed-services-offers.md)時，您包含的任何群組 (或使用者或服務主體) 都將具有和購買該方案的每個客戶相同的權限。 若要指派不同的群組以與每個客戶一起使用，您必須發佈每個客戶專屬的個別私人方案，或使用 Azure Resource Manager 範本個別讓客戶上線。 例如，您可以發佈存取非常有限的公用方案，然後使用自訂的 Azure Resource 範本來視需要授與額外的存取權，並直接與客戶合作以將其資源上線來取得額外的存取權。

## <a name="next-steps"></a>後續步驟

- [部署 Azure Multi-Factor Authentication](../../active-directory/authentication/howto-mfa-getstarted.md)。
- 了解[跨租用戶管理體驗](cross-tenant-management-experience.md)。
