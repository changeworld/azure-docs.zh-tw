---
title: 在 Azure AD 權利管理中要求存取套件的共用連結-Azure Active Directory
description: 瞭解如何在 Azure Active Directory 權利管理中共用連結，以要求存取套件。
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 32973d063c36a45eee79db0b4da4e7419cb70013
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87798541"
---
# <a name="share-link-to-request-an-access-package-in-azure-ad-entitlement-management"></a>在 Azure AD 權利管理中要求存取套件的共用連結

您目錄中的大部分使用者都可以登入我的存取權入口網站，並自動查看他們可以要求的存取套件清單。 不過，對於尚未存在於您目錄中的外部商務夥伴使用者，您必須將可用於要求存取套件的連結傳送給他們。 

只要 [外部使用者](entitlement-management-catalog-create.md) 的存取套件目錄已啟用，而且您有 [外部使用者目錄的原則](entitlement-management-access-package-request-policy.md)時，外部使用者就可以使用我的存取權入口網站連結來要求存取套件。

## <a name="share-link-to-request-an-access-package"></a>要求存取套件的共用連結

**必要角色：** 全域管理員、使用者系統管理員、目錄擁有者或存取套件管理員

1. 在 Azure 入口網站中按一下 [Azure Active Directory]  ，然後按一下 [身分識別治理]  。

1. 在左側功能表中，按一下 [ **存取套件** ]，然後開啟存取套件。

1. 在 [總覽] 頁面上，複製 **我的存取權入口網站] 連結**。

    ![存取套件概觀 - 我的存取權入口網站的連結](./media/entitlement-management-shared/my-access-portal-link.png)

    將整個我的存取權入口網站連結傳送給內部商務夥伴時，請務必複製該連結。 這可確保合作夥伴可以存取您目錄的入口網站以提出其要求。 連結會以開頭 `myaccess` ，包含目錄提示，並以存取套件識別碼結尾。  適用于美國政府的 (，我的存取權入口網站連結中的網域將會是 `myaccess.microsoft.us` 。 ) 

    `https://myaccess.microsoft.com/@<directory_hint>#/access-packages/<access_package_id>`

1. 傳送電子郵件，或將連結傳送給您的外部商務夥伴。 他們可以與使用者共用連結，以要求存取套件。

## <a name="next-steps"></a>後續步驟

- [要求存取存取套件](entitlement-management-request-access.md)
- [核准或拒絕存取要求](entitlement-management-request-approve.md)