---
title: 將現有的 Azure 訂用帳戶新增至您的租使用者-Azure AD
description: 以下相關指示說明如何將現有的 Azure 訂用帳戶新增至您的 Azure Active Directory 租用戶。
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 06/01/2020
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18, contperfq4
ms.collection: M365-identity-device-management
ms.openlocfilehash: bc81cca10bacc581e72c7caa70a0b74b7ae99f9a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85604211"
---
# <a name="associate-or-add-an-azure-subscription-to-your-azure-active-directory-tenant"></a>將 Azure 訂用帳戶關聯或新增至您的 Azure Active Directory 租用戶

Azure 訂用帳戶與 Azure Active Directory （Azure AD）之間具有信任關係。 訂用帳戶信任 Azure AD 來驗證使用者、服務和裝置。

多個訂用帳戶可以信任相同的 Azure AD 目錄。 每個訂用帳戶只能信任一個目錄。

若您的訂用帳戶已過期，您將無法再存取與訂用帳戶關聯的所有其他資源。 不過，Azure AD 目錄會保留在 Azure 中。 您可以使用不同的 Azure 訂用帳戶來建立和管理目錄的關聯。

您的所有使用者都有單一的*主*目錄可供驗證。 您的使用者也可以是其他目錄中的來賓。 您可以同時為 Azure AD 中的每個使用者使用主目錄與來賓目錄。

> [!Important]
> 當您將訂用帳戶與不同的目錄建立關聯時，具有使用[角色型存取控制（RBAC）](../../role-based-access-control/role-assignments-portal.md)指派之角色的使用者會失去其存取權。 傳統訂用帳戶管理員 (包括服務管理員和共同管理員) 也會失去存取權。
>
> 當訂用帳戶與不同目錄建立關聯時，也會從訂用帳戶移除原則指派。
>
> 將您的 Azure Kubernetes Service （AKS）叢集移至不同的訂用帳戶，或將叢集擁有的訂用帳戶移至新的租使用者，會導致叢集因為遺失角色指派和服務主體的許可權而失去功能。 如需 AKS 的詳細資訊，請參閱[Azure Kubernetes Service （AKS）](https://docs.microsoft.com/azure/aks/)。


## <a name="before-you-begin"></a>開始之前

您必須先執行下列工作，才能關聯或新增您的訂用帳戶：

- 請檢查下列在您建立或新增訂閱之後將發生的變更清單，以及您可能會受到的影響：

  - 已使用 RBAC 指派角色的使用者將會失去其存取權
  - 服務管理員和共同管理員將會失去存取權
  - 如果您有任何金鑰保存庫，它們將無法存取，而且您必須在關聯之後加以修正
  - 如果您有任何資源的受控識別，例如虛擬機器或 Logic Apps，您必須在關聯之後重新啟用或重新建立它們
  - 如果您有已註冊的 Azure Stack，您必須在關聯之後重新註冊它

- 使用符合下列條件的帳戶登入：

  - 具有訂用帳戶的[擁有](../../role-based-access-control/built-in-roles.md#owner)者角色指派。 如需如何指派擁有者角色的詳細資訊，請參閱[使用 RBAC 和 Azure 入口網站來管理 Azure 資源的存取權](../../role-based-access-control/role-assignments-portal.md)。
  - 存在於目前目錄和新目錄中。 目前目錄與訂用帳戶相關聯。 您會將新的目錄與訂用帳戶建立關聯。 如需取得另一個目錄存取權的詳細資訊，請參閱[在 Azure 入口網站中新增 AZURE ACTIVE DIRECTORY B2B](../b2b/add-users-administrator.md)共同作業使用者。

- 確定您不是使用 Azure 雲端服務提供者 (CSP) 訂用帳戶 (MS-AZR-0145P、MS-AZR-0146P、MS-AZR-159P)、Microsoft 內部訂用帳戶 (MS-AZR-0015P) 或 Microsoft Imagine 訂用帳戶 (MS-AZR-0144P)。

## <a name="associate-a-subscription-to-a-directory"></a>將訂用帳戶與目錄產生關聯<a name="to-associate-an-existing-subscription-to-your-azure-ad-directory"></a>

若要將現有的訂用帳戶關聯至您的 Azure AD 目錄，請遵循下列步驟：

1. 在[Azure 入口網站中](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)，從 [訂用帳戶] 頁面登入並選取您想要使用的訂用帳戶。

1. 選取 [**變更目錄**]。

    ![已反白顯示 [變更目錄] 選項的 [訂用帳戶] 頁面](media/active-directory-how-subscriptions-associated-directory/change-directory-in-azure-subscriptions.png)

1. 查看出現的任何警告，然後選取 [**變更**]。

    ![顯示要變更之目錄的 [變更目錄] 頁面](media/active-directory-how-subscriptions-associated-directory/edit-directory-ui.png)

    變更訂用帳戶的目錄之後，您會收到成功訊息。

  1. 選取 [訂用帳戶] 頁面上的 [**切換目錄**]，以移至您的新目錄。 

      ![目錄切換器頁面，包含範例資訊](media/active-directory-how-subscriptions-associated-directory/directory-switcher.png)

      可能需要幾個小時的時間，才能正確顯示所有專案。 如果看起來太長，請檢查**全域訂**用帳戶篩選。 請確定移動的訂用帳戶不是隱藏的。 您可能需要登出 Azure 入口網站並重新登入，才能看到新的目錄。

變更訂用帳戶目錄是服務層級作業，因此不會影響訂用帳戶帳單擁有權。 帳戶系統管理員仍然可以從[帳戶中心](https://account.azure.com/subscriptions)變更服務系統管理員。 若要刪除原始目錄，您必須將訂閱帳單擁有權轉移給新的帳戶管理員。若要深入瞭解如何傳送帳單擁有權，請參閱將[Azure 訂用帳戶的擁有權轉移給另一個帳戶](../../cost-management-billing/manage/billing-subscription-transfer.md)。

## <a name="post-association-steps"></a>後續關聯步驟

將訂用帳戶與不同的目錄建立關聯之後，您可能需要執行下列工作，才能繼續執行作業：

- 如果您有任何金鑰保存庫，您必須變更金鑰保存庫租使用者識別碼。 如需詳細資訊，請參閱在[訂用帳戶移動之後變更金鑰保存庫租使用者識別碼](../../key-vault/general/subscription-move-fix.md)。

- 如果您針對資源使用系統指派的受控識別，則必須重新啟用這些身分識別。 如果您使用使用者指派的受控識別，您必須重新建立這些身分識別。 重新啟用或重建受控識別之後，您必須重新建立指派給這些身分識別的許可權。 如需詳細資訊，請參閱[什麼是適用於 Azure 資源的受控識別？](../managed-identities-azure-resources/overview.md)。

- 如果您已使用此訂用帳戶註冊 Azure Stack，則必須重新註冊。 如需詳細資訊，請參閱[向 Azure 註冊 Azure Stack](/azure-stack/operator/azure-stack-registration)。

## <a name="next-steps"></a>後續步驟

- 若要建立新的 Azure AD 租使用者，請參閱[快速入門：在 Azure Active Directory 中建立新的租](active-directory-access-create-new-tenant.md)使用者。

- 若要深入瞭解 Microsoft Azure 如何控制資源存取，請參閱[傳統訂用帳戶管理員角色、AZURE RBAC 角色和 Azure AD 系統管理員角色](../../role-based-access-control/rbac-and-directory-admin-roles.md)。

- 若要深入瞭解如何在 Azure AD 中指派角色，請參閱[將系統管理員和非系統管理員角色指派給具有 Azure Active Directory 的使用者](active-directory-users-assign-role-azure-portal.md)。
