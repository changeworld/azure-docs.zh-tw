---
title: Azure 傳統訂用帳戶系統管理員 | Microsoft Docs
description: 說明如何新增或變更 Azure 共同管理員與服務管理員，以及如何檢視帳戶管理員。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/22/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 2493e893f9afda0642bd838c94538dd0b984bce5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79243480"
---
# <a name="azure-classic-subscription-administrators"></a>Azure 傳統訂用帳戶管理員

Microsoft 建議您使用角色型存取控制 (RBAC) 管理 Azure 資源存取權。 但是，如果您仍在使用經典部署模型，則需要使用經典的訂閱管理員角色：服務管理員和共同管理員。 如需詳細資訊，請參閱 [Azure Resource Manager 與傳統部署](../azure-resource-manager/management/deployment-models.md)。

本文說明如何新增或變更共同管理員與服務管理員，以及如何檢視帳戶管理員。

## <a name="add-a-co-administrator"></a>新增共同管理員

> [!TIP]
> 如果使用者需要管理 Azure 傳統部署，請透過使用 [Azure 服務管理 PowerShell 模組](https://docs.microsoft.com/powershell/module/servicemanagement/azure)新增共同管理員即可。 如果使用者僅使用 Azure 入口網站來管理傳統資源，則不需要針對使用者新增傳統系統管理員。

1. 以服務管理員或共同管理員身份登錄到[Azure 門戶](https://portal.azure.com)。

1. 開啟[訂用帳戶](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)，並選取訂用帳戶。

    共同管理員只能在訂用帳戶範圍內指派。

1. 按一下**存取控制 （IAM）。**

1. 按一下 [傳統管理員]**** 索引標籤。

    ![開啟傳統系統管理員的螢幕擷取畫面](./media/classic-administrators/classic-administrators.png)

1. 按一下**Add** > "**添加添加"共同管理員**以打開"添加共同管理員"窗格。

    如果 [新增共同管理員] 選項已停用，則表示您沒有權限。

1. 選擇要新增的使用者，然後按一下 [新增]****。

    ![新增共同管理員的螢幕擷取畫面](./media/classic-administrators/add-coadmin.png)

## <a name="add-a-guest-user-as-a-co-administrator"></a>將來賓使用者添加為共同管理員

要將來賓使用者添加為共同管理員，請按照與上[一個添加共同管理員](#add-a-co-administrator)部分的步驟相同的步驟。 來賓使用者必須滿足以下條件：

- 來賓使用者必須在目錄中具有狀態。 這意味著使用者被邀請到您的目錄並接受邀請。

有關如何將來賓使用者添加到目錄的詳細資訊，請參閱[在 Azure 門戶中添加 Azure 活動目錄 B2B 協作使用者](../active-directory/b2b/add-users-administrator.md)。

### <a name="differences-for-guest-users"></a>來賓使用者的差異

已分配了"共同管理員"角色的來賓使用者可能會看到與具有共同管理員角色的成員使用者相比的一些差異。 請考慮下列案例：

- 具有 Azure AD 帳戶的使用者 A（工作或學校帳戶）是 Azure 訂閱的服務管理員。
- 使用者 B 具有 Microsoft 帳戶。
- 使用者 A 將「共同管理員」角色指派給使用者 B。
- 使用者 B 可以執行幾乎所有操作，但無法在 Azure AD 目錄中註冊應用程式或查閱使用者。

您會預期使用者可以管理所有項目。 此差異的原因在於 Microsoft 帳戶是以來賓使用者而不是成員使用者身分新增至訂用帳戶。 在 Azure AD 中，來賓使用者與成員使用者具有不同的預設權限。 例如，成員使用者能夠在 Azure AD 中讀取其他使用者，來賓使用者不能。 成員使用者能夠在 Azure AD 中註冊新的服務主體，來賓使用者不能。

如果來賓使用者需要能夠執行這些工作，有一個可能的解決方案，就是指派來賓使用者所需的特定 Azure AD 系統管理員角色。 例如，在先前的案例中，您可以指派[目錄讀者](../active-directory/users-groups-roles/directory-assign-admin-roles.md#directory-readers)角色以讀取其他使用者，以及指派[應用程式開發人員](../active-directory/users-groups-roles/directory-assign-admin-roles.md#application-developer)角色，以便能夠建立服務主體。 如需有關成員使用者和來賓使用者及其權限的詳細資訊，請參閱 [Azure Active Directory 中的預設使用者權限是什麼？](../active-directory/fundamentals/users-default-permissions.md) 有關為來賓使用者授予存取權限的詳細資訊，請參閱[使用 RBAC 管理外部來賓使用者對 Azure 資源的訪問](role-assignments-external-users.md)。

請注意，[適用於 Azure 資源的內建角色](../role-based-access-control/built-in-roles.md)與 [Azure AD 系統管理員角色](../active-directory/users-groups-roles/directory-assign-admin-roles.md)不同。 內建的角色不會授與任何 Azure AD 存取權。 如需詳細資訊，請參閱[了解各種不同角色](../role-based-access-control/rbac-and-directory-admin-roles.md)。

有關比較成員使用者和來賓使用者的資訊，請參閱[Azure 活動目錄中的預設使用者許可權是什麼？](../active-directory/fundamentals/users-default-permissions.md)

## <a name="remove-a-co-administrator"></a>移除共同管理員

1. 以服務管理員或共同管理員身份登錄到[Azure 門戶](https://portal.azure.com)。

1. 開啟[訂用帳戶](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)，並選取訂用帳戶。

1. 按一下**存取控制 （IAM）。**

1. 按一下 [傳統管理員]**** 索引標籤。

1. 在要移除的共同管理員旁邊新增核取記號。

1. 按一下 [移除]****。

1. 在出現的訊息方塊中，按一下 [確定]****。

    ![移除共同管理員的螢幕擷取畫面](./media/classic-administrators/remove-coadmin.png)

## <a name="change-the-service-administrator"></a>變更服務管理員

只有帳戶管理員可以變更訂用帳戶的服務管理員。 根據預設，註冊 Azure 訂用帳戶時系統會將服務管理員與帳戶管理員設為同一人。 具有帳戶管理員角色的使用者無法存取 Azure 入口網站。 具有服務管理員角色的使用者可完整存取 Azure 入口網站。 如果帳戶管理員和服務管理員是相同的使用者，並且您將服務管理員變更為不同的使用者，則帳戶管理員將失去 Azure 入口網站的存取權限。 不過，帳戶管理員可隨時使用帳戶中心，將自己變更回服務管理員。

按照以下步驟更改**帳戶中心的**服務管理員。

### <a name="account-center"></a>帳戶中心

1. 通過檢查[更改服務管理員的限制](#limitations-for-changing-the-service-administrator)，確保方案受支援。

1. 以帳戶管理員身份登錄到[帳戶中心](https://account.windowsazure.com/subscriptions)。

1. 按一下訂用帳戶。

1. 在右側，按一下 **"編輯訂閱詳細資訊**"。

    ![顯示帳戶中心裡 [編輯訂用帳戶] 按鈕的螢幕擷取畫面](./media/classic-administrators/editsub.png)

1. 在 [服務管理員 **** ] 方塊中，輸入新的服務管理員的電子郵件地址。

    ![顯示應變更服務管理員電子郵件之方塊的螢幕擷取畫面](./media/classic-administrators/change-service-admin.png)

1. 按一下核取記號以儲存變更。

### <a name="limitations-for-changing-the-service-administrator"></a>變更服務管理員的限制

每個 Azure 訂閱只能有一個服務管理員。 更改服務管理員的行為會有所不同，具體取決於帳戶管理員是 Microsoft 帳戶還是 Azure AD 帳戶（工作帳戶還是學校帳戶）。

| 帳戶管理員帳戶 | 可以將服務管理員更改為其他 Microsoft 帳戶？ | 是否可以將服務管理員更改為同一目錄中的 Azure AD 帳戶？ | 可以將服務管理員更改為其他目錄中的 Azure AD 帳戶？ |
| --- | --- | --- | --- |
| Microsoft 帳戶 | 是 | 否 | 否 |
| Azure AD 帳戶 | 是 | 是 | 否 |

如果帳戶管理員是 Azure AD 帳戶，則可以將服務管理員更改為同一目錄中的 Azure AD 帳戶，但不能更改其他目錄中的服務管理員。 例如，abby@contoso.com可以將服務管理員更改為bob@contoso.com，但不能將服務管理員更改為john@notcontoso.com，除非john@notcontoso.comcontoso.com目錄中存在。

有關 Microsoft 帳戶和 Azure AD 帳戶的詳細資訊，請參閱[什麼是 Azure 活動目錄？](../active-directory/fundamentals/active-directory-whatis.md)

## <a name="view-the-account-administrator"></a>檢視帳戶管理員

帳戶管理員就是一開始註冊 Azure 訂用帳戶的使用者，也是負責訂用帳戶的計費擁有者。 要更改訂閱的帳戶管理員，請參閱將[Azure 訂閱的擁有權轉移到其他帳戶](../cost-management-billing/manage/billing-subscription-transfer.md)。

請遵循這些步驟以檢視帳戶管理員。

1. 登錄到 Azure[門戶](https://portal.azure.com)。

1. 開啟[訂用帳戶](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)，並選取訂用帳戶。

1. 按一下 **[屬性]**。

    訂閱的帳戶管理員將顯示在 **"帳戶管理員"** 框中。

    ![顯示帳戶系統管理員的螢幕擷取畫面](./media/classic-administrators/account-admin.png)

## <a name="next-steps"></a>後續步驟

* [了解 Azure 中的不同角色](../role-based-access-control/rbac-and-directory-admin-roles.md)
* [使用 RBAC 和 Azure 入口網站管理 Azure 資源的存取權](../role-based-access-control/role-assignments-portal.md)
* [添加或更改 Azure 訂閱管理員](../cost-management-billing/manage/add-change-subscription-administrator.md)
