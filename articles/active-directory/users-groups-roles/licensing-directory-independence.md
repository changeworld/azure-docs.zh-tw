---
title: 多個租使用者互動的特性-Azure AD |Microsoft Docs
description: 瞭解 Azure Active Directory 組織的資料獨立性
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: users-groups-roles
ms.date: 04/29/2020
ms.author: curtand
ms.custom: it-pro
ms.reviewer: sumitp
ms.collection: M365-identity-device-management
ms.openlocfilehash: bbe7b0211775e59504443d30fa253cfa14af13b1
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "82582751"
---
# <a name="understand-how-multiple-azure-active-directory-organizations-interact"></a>瞭解多個 Azure Active Directory 組織如何互動

在 Azure Active Directory （Azure AD）中，每個 Azure AD 組織都完全獨立：邏輯上獨立于您所管理之其他 Azure AD 組織的對等。 組織之間的這種獨立性包括資源獨立性、系統管理獨立性和同步處理獨立性。 組織之間沒有父子式關聯性。

## <a name="resource-independence"></a>資源獨立性

* 如果您在某個組織中建立或刪除 Azure AD 資源，它不會影響另一個組織中的任何資源，但外部使用者的部分例外。
* 如果您向某個組織註冊其中一個功能變數名稱，則任何其他組織都無法使用該名稱。

## <a name="administrative-independence"></a>系統管理獨立性

如果組織「Contoso」的非系統管理使用者建立了測試組織的「測試」，則：

* 根據預設，建立組織的使用者會新增為該新組織中的外部使用者，並在該組織中指派全域管理員角色。
* 組織「Contoso」的系統管理員對於組織的「測試」沒有直接的系統管理許可權，除非「測試」的管理員特別授與他們這些許可權。 不過，如果「Contoso」的系統管理員控制了建立「測試」的使用者帳戶，就可以控制對組織「測試」的存取權。
* 如果您在某個組織中新增或移除使用者的 Azure AD 角色，則變更不會影響使用者在任何其他 Azure AD 組織中指派的角色。

## <a name="synchronization-independence"></a>同步處理獨立性

您可以個別設定每個 Azure AD 組織，以從下列其中一個實例同步處理資料：

* Azure AD Connect 工具：可與單一 AD 樹系同步處理資料。
* Azure Active Directory Connector for Forefront Identity Manager，以與一或多個內部部署樹系和 (或) 非 Azure AD 資料來源同步處理資料。

## <a name="add-an-azure-ad-organization"></a>新增 Azure AD 組織

若要在 Azure 入口網站中新增 Azure AD 組織，請使用 Azure AD 全域管理員的帳戶登入 Azure 入口網站，然後選取[[](https://portal.azure.com) **新增**]。

> [!NOTE]
> 與其他 Azure 資源不同的是，您的 Azure AD 組織不是 Azure 訂用帳戶的子資源。 如果您的 Azure 訂用帳戶已取消或過期，您仍然可以使用 Azure PowerShell、Microsoft Graph API 或 Microsoft 365 系統管理中心來存取 Azure AD 組織的資料。 您也可以[將另一個訂用帳戶與組織產生關聯](../fundamentals/active-directory-how-subscriptions-associated-directory.md)。
>

## <a name="next-steps"></a>後續步驟

如 Azure AD 授權考慮和最佳作法，請參閱[什麼是 Azure Active Directory 授權？](../fundamentals/active-directory-licensing-whatis-azure-portal.md)。
