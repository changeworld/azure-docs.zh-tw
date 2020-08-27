---
title: 新增或變更 Azure 訂用帳戶系統管理員
description: 說明如何使用角色型存取控制 (RBAC) 來新增或變更 Azure 訂用帳戶管理員。
author: genlin
ms.reviewer: dcscontentpm
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: 2ccdb16af788f6f8d106628742b2a83e25b26263
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/20/2020
ms.locfileid: "88683091"
---
# <a name="add-or-change-azure-subscription-administrators"></a>新增或變更 Azure 訂用帳戶系統管理員


若要管理對 Azure 資源的存取，您必須具有適當的系統管理員角色。 Azure 有一個稱為 [Azure 角色型存取控制 (Azure RBAC)](../../role-based-access-control/overview.md) 的授權系統，其中含有可供您選擇的內建角色。 您可以在不同範圍 (例如管理群組、訂用帳戶或資源群組) 指派這些角色。 根據預設，建立新 Azure 訂用帳戶的人員可以將訂用帳戶的系統管理存取權指派給其他使用者。

本文說明如何在訂用帳戶範圍使用 RBAC 來新增或變更使用者的系統管理員角色。

Microsoft 建議您使用 RBAC 來管理對資源的存取。 不過，如果您仍是使用傳統部署模型，且使用 [Azure 服務管理 PowerShell 模組](/powershell/module/servicemanagement/azure.service) \(英文\) 來管理傳統資源，您會需要使用傳統系統管理員。

> [!TIP]
> 如果您只使用 Azure 入口網站來管理傳統資源，則不需要使用傳統系統管理員。

如需詳細資訊，請參閱 [Azure Resource Manager 與傳統部署](../../azure-resource-manager/management/deployment-models.md)和 [Azure 傳統訂用帳戶管理員](../../role-based-access-control/classic-administrators.md)。

<a name="add-an-admin-for-a-subscription"></a>

## <a name="assign-a-subscription-administrator"></a>指派訂用帳戶系統管理員

若要讓使用者成為 Azure 訂用帳戶的系統管理員，現有系統管理員應在訂用帳戶範圍為其指派[擁有者](../../role-based-access-control/built-in-roles.md#owner)角色 (Azure 角色)。 「擁有者」角色可授與使用者訂用帳戶中所有資源的完整存取權，包括將存取權委派給其他人的權限。 針對任何其他角色指派，這些步驟都相同。

如果您不確定誰是訂用帳戶的帳戶管理員，請使用下列步驟來找出帳戶管理員。

1. 開啟 [Azure 入口網站中的 [訂用帳戶] 頁面](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)。
1. 選取您想要檢查的訂用帳戶，然後查看 [設定]。
1. 選取 [屬性] 。 該訂用帳戶的帳戶管理員會顯示在 [帳戶管理員]  方塊中。

### <a name="to-assign-a-user-as-an-administrator"></a>將使用者指派為系統管理員

1. 以訂用帳戶擁有者身分登入 Azure 入口網站並開啟[訂用帳戶](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)。

1. 選擇您想要授與存取權的訂用帳戶。

1. 按一下 [存取控制 (IAM)]。

1. 按一下 [角色指派] 索引標籤以檢視此訂用帳戶的所有角色指派。

    ![顯示角色指派的螢幕擷取畫面](./media/add-change-subscription-administrator/role-assignments.png)

1. 按一下 [新增] > [新增角色指派]，以開啟 [新增角色指派] 窗格。

    如果您沒有指派角色的權限，此選項就會被停用。

1. 在 [角色] 下拉式清單中，選取 [擁有者] 角色。

1. 在 [選取] 清單中，選取使用者。 如果在清單中未看到使用者，您可以在 [選取] 方塊中輸入，以在目錄中搜尋顯示名稱與電子郵件地址。

    ![顯示已選取 [擁有者] 角色的螢幕擷取畫面](./media/add-change-subscription-administrator/add-role.png)

1. 按一下 [儲存] 以指派角色。

    在幾分鐘之後，即會在訂用帳戶範圍將「擁有者」角色指派給使用者。

## <a name="next-steps"></a>後續步驟

* [什麼是 Azure 角色型存取控制 (Azure RBAC)？](../../role-based-access-control/overview.md)
* [了解 Azure 中的不同角色](../../role-based-access-control/rbac-and-directory-admin-roles.md)
* [將 Azure 訂用帳戶關聯或新增至您的 Azure Active Directory 租用戶](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)
* [Azure Active Directory 中的系統管理員角色權限](../../active-directory/users-groups-roles/directory-assign-admin-roles.md)

## <a name="need-help-contact-support"></a>需要協助嗎？ 請連絡支援人員

如果仍需要協助，請[連絡支援人員](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解決您的問題。
