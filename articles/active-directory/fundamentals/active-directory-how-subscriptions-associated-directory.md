---
title: 將現有 Azure 訂閱添加到租戶 - Azure AD
description: 以下相關指示說明如何將現有的 Azure 訂用帳戶新增至您的 Azure Active Directory 租用戶。
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: b8f44e9a1e43da2b9ce6c817898c1722fba715c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262174"
---
# <a name="associate-or-add-an-azure-subscription-to-your-azure-active-directory-tenant"></a>將 Azure 訂用帳戶關聯或新增至您的 Azure Active Directory 租用戶

Azure 訂閱與 Azure 活動目錄 （Azure AD） 具有信任關係。 訂閱信任 Azure AD 以對使用者、服務和設備進行身份驗證。

多個訂閱可以信任同一 Azure AD 目錄。 每個訂閱只能信任單個目錄。

若您的訂用帳戶已過期，您將無法再存取與訂用帳戶關聯的所有其他資源。 但是，Azure AD 目錄仍保留在 Azure 中。 您可以使用其他 Azure 訂閱關聯和管理目錄。

所有使用者都有一個*主*目錄進行身份驗證。 您的使用者也可以成為其他目錄中的來賓。 您可以同時為 Azure AD 中的每個使用者使用主目錄與來賓目錄。

> [!Important]
> 將訂閱關聯到其他目錄時，具有使用[基於角色的存取控制 （RBAC）](../../role-based-access-control/role-assignments-portal.md)分配角色的使用者將失去存取權限。 傳統訂用帳戶管理員 (包括服務管理員和共同管理員) 也會失去存取權。
>
> 當訂用帳戶與不同目錄建立關聯時，也會從訂用帳戶移除原則指派。
>
> 將 Azure Kubernetes 服務 （AKS） 群集移動到其他訂閱，或將群集所屬訂閱移動到新租戶，會導致群集由於角色指派和服務主體許可權丟失而喪失功能。 有關 AKS 的詳細資訊，請參閱[Azure 庫伯奈斯服務 （AKS）。](https://docs.microsoft.com/azure/aks/)


## <a name="before-you-begin"></a>開始之前

在關聯或添加訂閱之前，請執行以下任務：

- 查看以下更改清單以及可能受影響的操作方式：

  - 已使用 RBAC 分配角色的使用者將失去存取權限
  - 服務管理員和共同管理員將失去存取權限
  - 如果您有任何金鑰保存庫，它們將無法訪問，您必須在關聯後修復它們
  - 如果資源（如虛擬機器或邏輯應用）具有任何託管標識，則必須在關聯後重新啟用或重新創建它們
  - 如果您有註冊的 Azure 堆疊，則在關聯後必須重新註冊

- 使用符合下列條件的帳戶登入：

  - 具有訂閱[的擁有者](../../role-based-access-control/built-in-roles.md#owner)角色指派。 有關如何分配擁有者角色的資訊，請參閱[使用 RBAC 和 Azure 門戶管理對 Azure 資源的訪問](../../role-based-access-control/role-assignments-portal.md)。
  - 存在於目前的目錄和新目錄中。 目前的目錄與訂閱相關聯。 您將新目錄與訂閱相關聯。 有關訪問其他目錄的詳細資訊，請參閱在 Azure[門戶中添加 Azure 活動目錄 B2B 協作使用者](../b2b/add-users-administrator.md)。

- 確定您不是使用 Azure 雲端服務提供者 (CSP) 訂用帳戶 (MS-AZR-0145P、MS-AZR-0146P、MS-AZR-159P)、Microsoft 內部訂用帳戶 (MS-AZR-0015P) 或 Microsoft Imagine 訂用帳戶 (MS-AZR-0144P)。

## <a name="associate-a-subscription-to-a-directory"></a>將訂閱關聯到目錄<a name="to-associate-an-existing-subscription-to-your-azure-ad-directory"></a>

要將現有訂閱關聯到 Azure AD 目錄，請按照以下步驟操作：

1. 在 [Azure 入口網站的 [訂用帳戶] 頁面](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)登入並選取您要使用的訂用帳戶。

1. 按一下 [變更目錄]****。

    ![已反白顯示 [變更目錄] 選項的 [訂用帳戶] 頁面](media/active-directory-how-subscriptions-associated-directory/change-directory-in-azure-subscriptions.png)

1. 檢閱出現的任何警告，然後選取 [變更]****。

    ![顯示要變更之目錄的 [變更目錄] 頁面](media/active-directory-how-subscriptions-associated-directory/edit-directory-ui.png)

    訂用帳戶的目錄會變更， 而且您會看到成功訊息。

    ![關於目錄更改的成功消息](media/active-directory-how-subscriptions-associated-directory/edit-directory-success.png)

使用**Switch 目錄**轉到新目錄。 一切可能需要幾個小時才能正確顯示。 如果似乎時間過長，請查看**全域訂閱篩選器**。 確保移動的訂閱未隱藏。 您可能需要登出 Azure 門戶並重新登錄才能看到新目錄。

![目錄切換器頁面，包含示例資訊](media/active-directory-how-subscriptions-associated-directory/directory-switcher.png)

變更訂用帳戶目錄是服務層級作業，因此不會影響訂用帳戶帳單擁有權。 帳戶系統管理員仍然可以從[帳戶中心](https://account.azure.com/subscriptions)變更服務系統管理員。 要刪除原始目錄，必須將訂閱計費擁有權轉讓給新的帳戶管理員。要瞭解有關轉移計費擁有權的更多詳細資訊，請參閱[將 Azure 訂閱的擁有權轉移到其他帳戶](../../cost-management-billing/manage/billing-subscription-transfer.md)。

## <a name="post-association-steps"></a>關聯後步驟

將訂閱關聯到其他目錄後，可能需要執行以下任務才能恢復操作：

- 如果您有任何金鑰保存庫，則必須更改金鑰保存庫租戶 ID。 有關詳細資訊，請參閱[在訂閱移動後更改金鑰保存庫租戶 ID。](../../key-vault/key-vault-subscription-move-fix.md)

- 如果為資源使用系統分配的託管標識，則必須重新啟用這些標識。 如果使用使用者分配的託管標識，則必須重新創建這些標識。 重新啟用或重新創建託管標識後，必須重新建立分配給這些標識的許可權。 有關詳細資訊，請參閱[Azure 資源的託管標識是什麼？](../managed-identities-azure-resources/overview.md)

- 如果已使用此訂閱註冊 Azure 堆疊，則必須重新註冊。 有關詳細資訊，請參閱將[Azure 堆疊註冊到 Azure](/azure-stack/operator/azure-stack-registration)。

## <a name="next-steps"></a>後續步驟

- 要創建新的 Azure AD 租戶，請參閱[快速入門：在 Azure 活動目錄中創建新租戶](active-directory-access-create-new-tenant.md)。

- 要瞭解有關 Microsoft Azure 如何控制資源訪問的更多資訊，請參閱[經典訂閱管理員角色、Azure RBAC 角色和 Azure AD 管理員角色](../../role-based-access-control/rbac-and-directory-admin-roles.md)。

- 要瞭解有關如何在 Azure AD 中分配角色，請參閱[將管理員和非管理員角色分配給具有 Azure 活動目錄 的使用者](active-directory-users-assign-role-azure-portal.md)。
