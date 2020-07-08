---
title: 如何將使用者指派至應用程式 | Microsoft Docs
description: 了解如何將使用者指派至租用戶中的應用程式
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: 45124862fffa3a1ef6f601733407fbbea4eb5e74
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84762950"
---
# <a name="how-to-assign-users-to-applications"></a>將使用者指派至應用程式

本文協助您了解如何將使用者指派至租用戶中的應用程式。

## <a name="how-do-users-get-assigned-to-an-application-in-azure-ad"></a>如何將使用者指派至 Azure AD 中的應用程式？

若要讓使用者存取應用程式，必須先設法將他們指派至應用程式。 可以由系統管理員、企業代理人，或有時是使用者本身來指派。 以下描述將使用者指派至應用程式的方法︰

1.  系統管理員直接[指派使用者](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)至應用程式

2.  系統管理員[指派群組](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) (使用者所屬的群組) 至應用程式，包括︰

    * 已從內部部署同步的群組

    * 在雲端中建立的靜態安全性群組

    * 在雲端中建立的[動態安全性群組](https://docs.microsoft.com/azure/active-directory/active-directory-groups-dynamic-membership-azure-portal)

    * 在雲端中建立的 Office 365 群組

    * [所有使用者](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-dedicated-groups)群組

3.  系統管理員啟用[自助應用程式存取](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access)，以允許使用者利用[應用程式存取面板的](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) **新增應用程式**功能新增應用程式，而**不需要商務核准**

4.  系統管理員啟用[自助應用程式存取](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access)，以允許使用者利用[應用程式存取面板的](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) **新增應用程式**功能新增應用程式，但需要經過**一群選定商務核准者的事先核准**

5.  系統管理員啟用[自助群組管理](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management)，以允許使用者加入已指派應用程式的群組，而**不需要商務核准**

6.  系統管理員啟用[自助群組管理](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management)，以允許使用者加入已指派應用程式的群組，但需要經過**一群選定商務核准者的事先核准**

7.  系統管理員將第一方應用程式 (例如 [Microsoft Office 365](https://products.office.com/)) 的授權直接指派給使用者

8.  系統管理員將第一方應用程式 (例如 [Microsoft Office 365](https://products.office.com/)) 的授權指派給使用者所屬的群組

9.  [系統管理員同意應用程式](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview)供所有使用者使用，然後使用者登入應用程式

10. 使用者本身登入應用程式以[同意應用程式](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview)

## <a name="next-steps"></a>後續步驟
[使用 Azure Active Directory 來管理應用程式](what-is-application-management.md)
