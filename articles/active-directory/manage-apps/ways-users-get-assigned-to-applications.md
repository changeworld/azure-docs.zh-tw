---
title: 瞭解如何在 Azure Active Directory 中將使用者指派給應用程式
description: 瞭解如何將使用者指派給使用 Azure Active Directory 進行身分識別管理的應用程式。
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: kenwith
ms.openlocfilehash: 243e5ece0d0a14cb7e3ade409ee68510cef64a9c
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/02/2020
ms.locfileid: "89397095"
---
# <a name="understand-how-users-are-assigned-to-apps-in-azure-active-directory"></a>瞭解如何在 Azure Active Directory 中將使用者指派給應用程式
本文協助您了解如何將使用者指派至租用戶中的應用程式。

## <a name="how-do-users-get-assigned-to-an-application-in-azure-ad"></a>如何將使用者指派至 Azure AD 中的應用程式？
若要讓使用者存取應用程式，必須先設法將他們指派至應用程式。 可以由系統管理員、企業代理人，或有時是使用者本身來指派。 以下描述將使用者指派至應用程式的方法︰

*  系統管理員直接[指派使用者](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)至應用程式
*  系統管理員[指派群組](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) (使用者所屬的群組) 至應用程式，包括︰
    * 已從內部部署同步的群組
    * 在雲端中建立的靜態安全性群組
    * 在雲端中建立的[動態安全性群組](https://docs.microsoft.com/azure/active-directory/active-directory-groups-dynamic-membership-azure-portal)
    * 在雲端中建立的 Office 365 群組
    * [所有使用者](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-dedicated-groups)群組
*  系統管理員可啟用[自助應用程式存取](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access)，以允許使用者使用[我的應用程式](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)**新增**應用程式功能來新增應用程式，**而不需要商務核准**
*  系統管理員可啟用[自助應用程式存取](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access)，以允許使用者使用[我的應用程式](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)**新增應用**程式功能來新增應用程式，但只需要**一組所選商務核准者的先前核准**（w）
*  系統管理員啟用[自助群組管理](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management)，以允許使用者加入已指派應用程式的群組，而**不需要商務核准**
*  系統管理員啟用[自助群組管理](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management)，以允許使用者加入已指派應用程式的群組，但需要經過**一群選定商務核准者的事先核准**
*  系統管理員將第一方應用程式 (例如 [Microsoft Office 365](https://products.office.com/)) 的授權直接指派給使用者
*  系統管理員將第一方應用程式 (例如 [Microsoft Office 365](https://products.office.com/)) 的授權指派給使用者所屬的群組
*  [系統管理員同意應用程式](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview)供所有使用者使用，然後使用者登入應用程式
* 使用者本身登入應用程式以[同意應用程式](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview)

## <a name="next-steps"></a>後續步驟
* [應用程式管理快速入門系列](view-applications-portal.md)
* [什麼是應用程式管理？](what-is-application-management.md)
* [什麼是單一登入？](what-is-single-sign-on.md)
