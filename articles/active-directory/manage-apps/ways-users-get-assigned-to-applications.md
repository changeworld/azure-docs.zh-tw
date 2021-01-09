---
title: 瞭解如何在 Azure Active Directory 中將使用者指派給應用程式
description: 瞭解如何將使用者指派給使用 Azure Active Directory 進行身分識別管理的應用程式。
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: reference
ms.date: 01/07/2021
ms.author: kenwith
ms.openlocfilehash: aedcecf528f07d9d7f89c1d5659d8fcd2cb36df2
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2021
ms.locfileid: "98016723"
---
# <a name="understand-how-users-are-assigned-to-apps-in-azure-active-directory"></a>瞭解如何在 Azure Active Directory 中將使用者指派給應用程式
本文協助您了解如何將使用者指派至租用戶中的應用程式。

## <a name="how-do-users-get-assigned-to-an-application-in-azure-ad"></a>如何將使用者指派至 Azure AD 中的應用程式？
若要讓使用者存取應用程式，必須先設法將他們指派至應用程式。 可以由系統管理員、企業代理人，或有時是使用者本身來指派。 以下描述將使用者指派至應用程式的方法︰

*  系統管理員直接[指派使用者](./assign-user-or-group-access-portal.md)至應用程式
*  系統管理員[指派群組](./assign-user-or-group-access-portal.md) (使用者所屬的群組) 至應用程式，包括︰
    * 已從內部部署同步的群組
    * 在雲端中建立的靜態安全性群組
    * 在雲端中建立的[動態安全性群組](../enterprise-users/groups-dynamic-membership.md)
    * 在雲端中建立的 Microsoft 365 群組
    * [所有使用者](../fundamentals/active-directory-groups-create-azure-portal.md)群組
*  系統管理員可啟用 [自助應用程式存取](./manage-self-service-access.md)，以允許使用者使用 [我的應用程式](../user-help/my-apps-portal-end-user-access.md)**新增** 應用程式功能來新增應用程式，**而不需要商務核准**
*  系統管理員可啟用 [自助應用程式存取](./manage-self-service-access.md)，以允許使用者使用 [我的應用程式](../user-help/my-apps-portal-end-user-access.md)**新增應用** 程式功能來新增應用程式，但僅限於 **一組選定商務核准者的事先核准**
*  系統管理員啟用 [自助群組管理](../enterprise-users/groups-self-service-management.md)，以允許使用者加入已指派應用程式的群組，而 **不需要商務核准**
*  系統管理員啟用 [自助群組管理](../enterprise-users/groups-self-service-management.md)，以允許使用者加入已指派應用程式的群組，但需要經過 **一群選定商務核准者的事先核准**
*  系統管理員將第一方應用程式的授權直接指派給使用者，例如 [Microsoft 365](https://products.office.com/)
*  系統管理員會將授權指派給使用者所屬的群組，例如 [Microsoft 365](https://products.office.com/)
*  [系統管理員同意應用程式](../develop/howto-convert-app-to-be-multi-tenant.md)供所有使用者使用，然後使用者登入應用程式
* 使用者本身登入應用程式以[同意應用程式](../develop/howto-convert-app-to-be-multi-tenant.md)

## <a name="next-steps"></a>後續步驟
* [應用程式管理快速入門系列](view-applications-portal.md)
* [什麼是應用程式管理？](what-is-application-management.md)
* [什麼是單一登入？](what-is-single-sign-on.md)