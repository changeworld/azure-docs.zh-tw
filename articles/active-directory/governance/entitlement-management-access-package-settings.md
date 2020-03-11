---
title: '[共用連結] 可在 Azure AD 的權利管理中要求存取套件-Azure Active Directory'
description: 瞭解如何在 Azure Active Directory 權利管理中共用連結以要求存取封裝。
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/15/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: ea90032b1f0cfe598ffdb3d35448a996f3111036
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2020
ms.locfileid: "78968751"
---
# <a name="share-link-to-request-an-access-package-in-azure-ad-entitlement-management"></a>Azure AD 權利管理中的共用連結以要求存取套件

您目錄中的大部分使用者都可以登入我的存取權入口網站，並自動查看他們可以要求的存取套件清單。 不過，對於還不在您目錄中的外部商業夥伴使用者，您將需要傳送一個連結，讓他們可以用來要求存取封裝。 

只要針對[外部使用者啟用](entitlement-management-catalog-create.md)存取套件的目錄，而且您有[外部使用者目錄的原則](entitlement-management-access-package-request-policy.md)，外部使用者就可以使用我的存取權入口網站連結來要求存取套件。

## <a name="share-link-to-request-an-access-package"></a>共用連結以要求存取套件

**先決條件角色：** 全域管理員、使用者系統管理員、目錄擁有者或存取套件管理員

1. 在 Azure 入口網站中按一下 [Azure Active Directory]，然後按一下 [身分識別治理]。

1. 在左側功能表中，按一下 [**存取套件**]，然後開啟存取封裝。

1. 在 [總覽] 頁面上，複製 [**我的存取權入口網站] 連結**。

    ![存取套件概觀 - 我的存取權入口網站的連結](./media/entitlement-management-shared/my-access-portal-link.png)

    將整個我的存取權入口網站連結傳送給內部商業夥伴時，請務必將它複製。 這可確保合作夥伴可以存取您目錄的入口網站來提出要求。 此連結會以 `myaccess`開頭，包括目錄提示，並以存取套件識別碼結尾。  （針對美國政府，我的存取權入口網站連結中的網域將會 `myaccess.microsoft.us`）。

    `https://myaccess.microsoft.com/@<directory_hint>#/access-packages/<access_package_id>`

1. 傳送電子郵件給您的外部商業夥伴，或將其連結。 他們可以與使用者共用連結，以要求存取封裝。

## <a name="next-steps"></a>後續步驟

- [要求存取權套件](entitlement-management-request-access.md)
- [核准或拒絕存取要求](entitlement-management-request-approve.md)