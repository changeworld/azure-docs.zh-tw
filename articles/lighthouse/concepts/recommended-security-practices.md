---
title: 建議的安全性作法
description: 使用 Azure 委派的資源管理時，必須考慮安全性和存取控制。
ms.date: 03/24/2020
ms.topic: conceptual
ms.openlocfilehash: d9b806aaf988fedfde6ce468f3eff948aa8ce344
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246903"
---
# <a name="recommended-security-practices"></a>建議的安全性作法

使用[Azure 委派的資源管理](azure-delegated-resource-management.md)時，必須考慮安全性和存取控制。 租戶中的使用者將直接存取客戶訂閱和資源組，因此您需要採取措施維護租戶的安全性。 您還需要確保僅允許有效管理客戶資源所需的存取權限。 此主題會提供可協助您這樣做的建議。

## <a name="require-azure-multi-factor-authentication"></a>要求使用 Azure Multi-Factor Authentication

[Azure 多重要素驗證](../../active-directory/authentication/concept-mfa-howitworks.md)（也稱為兩步驗證）通過要求多個身份驗證步驟有助於防止攻擊者存取帳戶。 您應該針對服務提供者租用戶中的所有使用者要求使用 Multi-Factor Authentication，這包括將能夠存取客戶資源的所有使用者。

我們建議您要求客戶也在其租用戶中實作 Azure Multi-Factor Authentication。

## <a name="assign-permissions-to-groups-using-the-principle-of-least-privilege"></a>使用最低權限原則將權限指派給群組

為了簡化管理，我們建議對管理客戶資源所需的每個角色使用 Azure AD 使用者組。 這能讓您視需要將個別使用者新增至群組，是或從該群組中移除使用者，而不是直接將權限指派給該使用者。

> [!IMPORTANT]
> 為了添加 Azure AD 組的許可權，**組類型**必須是 **"安全"，** 而不是**Office 365**。 創建組時選擇此選項。 如需詳細資訊，請參閱[使用 Azure Active Directory 建立基本群組並新增成員](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)。

建立權限結構時，請務必遵循最低權限原則來讓使用者只具備完成其工作所需的權限，以協助減少發生意外錯誤的機會。

例如，您應該使用像這樣的結構：

|群組名稱  |類型  |principalId  |角色定義  |角色定義識別碼  |
|---------|---------|---------|---------|---------|
|結構設計師     |使用者群組         |\<principalId\>         |參與者         |b24988ac-6180-42a0-ab88-20f7382dd24c  |
|評量     |使用者群組         |\<principalId\>         |讀取者         |acdd72a7-3385-48ef-bd42-f606fba81ae7  |
|VM 專家     |使用者群組         |\<principalId\>         |VM 參與者         |9980e02c-c2be-4d73-94e8-173b1dc7cf3c  |
|自動化     |服務主體名稱 (SPN)         |\<principalId\>         |參與者         |b24988ac-6180-42a0-ab88-20f7382dd24c  |

創建這些組後，可以根據需要分配使用者。 請僅加入真正需要存取權的使用者。 請務必定期檢閱群組成員，並移除已不再適合或需要包含在該群組內的任何使用者。

請注意，當您[透過公用受控服務供應項目將客戶上線](../how-to/publish-managed-services-offers.md)時，您包含的任何群組 (或使用者或服務主體) 都將具有和購買該方案的每個客戶相同的權限。 要分配不同的組以與每個客戶一起使用，您需要使用 Azure 資源管理器範本發佈每個客戶或板載客戶獨有的單獨私有計劃。 例如，您可以發佈存取非常有限的公用方案，然後使用自訂的 Azure Resource 範本來視需要授與額外的存取權，並直接與客戶合作以將其資源上線來取得額外的存取權。


## <a name="next-steps"></a>後續步驟

- [部署 Azure 多重要素驗證](../../active-directory/authentication/howto-mfa-getstarted.md)。
- 了解[跨租用戶管理體驗](cross-tenant-management-experience.md)。
