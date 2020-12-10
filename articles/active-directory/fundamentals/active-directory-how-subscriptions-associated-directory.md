---
title: 將現有的 Azure 訂用帳戶新增至您的租使用者-Azure AD
description: 以下相關指示說明如何將現有的 Azure 訂用帳戶新增至您的 Azure Active Directory 租用戶。
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 09/01/2020
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18, contperf-fy20q4
ms.collection: M365-identity-device-management
ms.openlocfilehash: c7a39340f44e2c6eeae5b5f1a8e687bc73b3b0fe
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/10/2020
ms.locfileid: "97028405"
---
# <a name="associate-or-add-an-azure-subscription-to-your-azure-active-directory-tenant"></a>將 Azure 訂用帳戶關聯或新增至您的 Azure Active Directory 租用戶

Azure 訂用帳戶與 Azure Active Directory (Azure AD) 有信任關係。 訂用帳戶信任 Azure AD 來驗證使用者、服務和裝置。

多個訂用帳戶可以信任相同的 Azure AD 目錄。 每個訂用帳戶只能信任一個目錄。

一或多個 Azure 訂用帳戶可以與 Azure Active Directory (Azure AD) 的實例建立信任關係，以針對 Azure 服務驗證和授權安全性主體和裝置。  當訂閱到期時，Azure AD 服務的受信任實例仍會保留，但安全性主體會失去對 Azure 資源的存取權。

當使用者註冊 Microsoft 雲端服務時，會建立新的 Azure AD 租使用者，且使用者會成為全域管理員角色的成員。 不過，當訂用帳戶的擁有者將其訂用帳戶加入至現有的租使用者時，不會將擁有者指派給全域管理員角色。

您所有的使用者都有單一 *主* 目錄來進行驗證。 您的使用者也可以是其他目錄中的來賓。 您可以同時為 Azure AD 中的每個使用者使用主目錄與來賓目錄。

> [!Important]
> 當您將訂用帳戶與不同的目錄建立關聯時，已使用 [Azure 角色型存取控制](../../role-based-access-control/role-assignments-portal.md) 指派角色的使用者會失去其存取權。 傳統訂用帳戶管理員 (包括服務管理員和共同管理員) 也會失去存取權。
>
> 將 Azure Kubernetes Service (AKS) 叢集移至不同的訂用帳戶，或將叢集擁有的訂用帳戶移至新的租使用者，會導致叢集因為遺失角色指派和服務主體的許可權而遺失功能。 如需 AKS 的詳細資訊，請參閱 [Azure Kubernetes Service (AKS) ](../../aks/index.yml)。

## <a name="before-you-begin"></a>開始之前

請先執行下列工作，才能建立或新增您的訂用帳戶：

- 請參閱下列變更清單，這些變更會在您關聯或新增訂閱之後發生，以及您可能會受到影響的方式：

  - 已使用 Azure RBAC 指派角色的使用者將會失去其存取權
  - 服務管理員和 Co-Administrators 將會失去存取權
  - 如果您有任何金鑰保存庫，將無法存取它們，而且您必須在關聯之後修正它們
  - 如果您的資源有任何受控識別，例如虛擬機器或 Logic Apps，則您必須在關聯之後重新啟用或重新建立它們
  - 如果您有已註冊的 Azure Stack，您必須在關聯之後重新註冊它
  - 如需詳細資訊，請參閱[將 Azure 訂用帳戶轉移至不同的 Azure AD 目錄](../../role-based-access-control/transfer-subscription.md)。

- 使用符合下列條件的帳戶登入：

  - 具有訂用帳戶的 [擁有](../../role-based-access-control/built-in-roles.md#owner) 者角色指派。 如需有關如何指派擁有者角色的詳細資訊，請參閱 [使用 Azure 入口網站新增或移除 Azure 角色指派](../../role-based-access-control/role-assignments-portal.md)。
  - 存在於目前目錄和新目錄中。 目前的目錄與訂用帳戶相關聯。 您會將新的目錄與訂用帳戶產生關聯。 如需取得其他目錄存取權的詳細資訊，請參閱 [Azure 入口網站中的新增 AZURE ACTIVE DIRECTORY B2B](../external-identities/add-users-administrator.md)共同作業使用者。

- 確定您不是使用 Azure 雲端服務提供者 (CSP) 訂用帳戶 (MS-AZR-0145P、MS-AZR-0146P、MS-AZR-159P)、Microsoft 內部訂用帳戶 (MS-AZR-0015P) 或 Microsoft Imagine 訂用帳戶 (MS-AZR-0144P)。

## <a name="associate-a-subscription-to-a-directory"></a>將訂用帳戶與目錄產生關聯<a name="to-associate-an-existing-subscription-to-your-azure-ad-directory"></a>

若要將現有的訂用帳戶與您的 Azure AD 目錄建立關聯，請遵循下列步驟：

1. 在 [Azure 入口網站中](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)，從 [訂閱] 頁面登入並選取您要使用的訂用帳戶。

1. 選取 [ **變更目錄**]。

   ![已反白顯示 [變更目錄] 選項的 [訂用帳戶] 頁面](media/active-directory-how-subscriptions-associated-directory/change-directory-in-azure-subscriptions.png)

1. 檢查出現的任何警告，然後選取 [ **變更**]。

   ![顯示要變更之目錄的 [變更目錄] 頁面](media/active-directory-how-subscriptions-associated-directory/edit-directory-ui.png)

   變更訂用帳戶的目錄之後，您會收到成功訊息。

1. 選取 [訂用帳戶] 頁面上的 [ **切換目錄** ]，移至您的新目錄。

   ![包含範例資訊的目錄切換器頁面](media/active-directory-how-subscriptions-associated-directory/directory-switcher.png)

   所有專案可能需要數小時才能正確顯示。 如果似乎花費太長時間，請檢查 **全域訂閱篩選準則**。 請確定移動的訂用帳戶不會隱藏。 您可能需要登出 Azure 入口網站並重新登入，才能看到新的目錄。

變更訂用帳戶目錄是服務層級作業，因此不會影響訂用帳戶帳單擁有權。 若要刪除原始目錄，您必須將訂用帳戶帳單擁有權轉移給新的帳戶管理員。若要深入瞭解如何轉移帳單擁有權，請參閱將 [Azure 訂用帳戶的擁有權轉移給另一個帳戶](../../cost-management-billing/manage/billing-subscription-transfer.md)。

## <a name="post-association-steps"></a>後續關聯步驟

將訂用帳戶與不同的目錄建立關聯之後，您可能需要執行下列工作以繼續作業：

- 如果您有任何金鑰保存庫，您必須變更金鑰保存庫租使用者識別碼。 如需詳細資訊，請參閱 [在訂用帳戶移動之後變更金鑰保存庫租使用者識別碼](../../key-vault/general/move-subscription.md)。

- 如果您針對資源使用系統指派的受控識別，則必須重新啟用這些身分識別。 如果您使用使用者指派的受控識別，則必須重新建立這些身分識別。 重新啟用或重新建立受控識別之後，您必須重新建立指派給這些身分識別的許可權。 如需詳細資訊，請參閱[什麼是適用於 Azure 資源的受控識別？](../managed-identities-azure-resources/overview.md)。

- 如果您已使用此訂用帳戶註冊 Azure Stack，則必須重新註冊。 如需詳細資訊，請參閱 [向 Azure 註冊 Azure Stack](/azure-stack/operator/azure-stack-registration)。

- 如需詳細資訊，請參閱[將 Azure 訂用帳戶轉移至不同的 Azure AD 目錄](../../role-based-access-control/transfer-subscription.md)。

## <a name="next-steps"></a>後續步驟

- 若要建立新的 Azure AD 租使用者，請參閱 [快速入門：在 Azure Active Directory 中建立新的租](active-directory-access-create-new-tenant.md)使用者。

- 若要深入瞭解 Microsoft Azure 如何控制資源存取，請參閱 [傳統訂用帳戶管理員角色、Azure 角色和 Azure AD 管理員角色](../../role-based-access-control/rbac-and-directory-admin-roles.md)。

- 若要深入瞭解如何在 Azure AD 中指派角色，請參閱 [將系統管理員和非系統管理員角色指派給具有 Azure Active Directory 的使用者](active-directory-users-assign-role-azure-portal.md)。
