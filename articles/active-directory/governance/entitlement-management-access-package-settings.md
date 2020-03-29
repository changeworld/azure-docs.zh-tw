---
title: 共用連結以請求 Azure AD 授權管理中的訪問包 - Azure 活動目錄
description: 瞭解如何共用連結以在 Azure 活動目錄授權管理中請求訪問包。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78968751"
---
# <a name="share-link-to-request-an-access-package-in-azure-ad-entitlement-management"></a>共用連結以在 Azure AD 授權管理中請求訪問包

目錄中的大多數使用者可以登錄到"我的訪問"門戶，並自動查看他們可以請求的訪問包清單。 但是，對於尚未在目錄中的外部業務合作夥伴使用者，您需要向他們發送一個連結，他們可以使用該連結請求訪問包。 

只要[為外部使用者啟用](entitlement-management-catalog-create.md)了訪問包的目錄，並且您有[外部使用者目錄的策略](entitlement-management-access-package-request-policy.md)，外部使用者可以使用"我的訪問"門戶連結請求訪問包。

## <a name="share-link-to-request-an-access-package"></a>共用連結以請求訪問包

**先決條件角色：** 全域管理員、使用者管理員、目錄擁有者或訪問包管理器

1. 在 Azure 入口網站中按一下 [Azure Active Directory]****，然後按一下 [身分識別治理]****。

1. 在左側功能表中，按一下 **"訪問包**"，然後打開訪問包。

1. 在"概述"頁上，複製 **"我的訪問"門戶連結**。

    ![存取套件概觀 - 我的存取權入口網站的連結](./media/entitlement-management-shared/my-access-portal-link.png)

    將整個"我的訪問"門戶連結發送給內部業務合作夥伴時，請務必複製該連結。 這可確保合作夥伴將訪問目錄的門戶以發出請求。 連結以 開頭`myaccess`，包括目錄提示，以訪問包 ID 結尾。  （對於美國政府，"我的訪問"門戶連結中的域將為`myaccess.microsoft.us`.）

    `https://myaccess.microsoft.com/@<directory_hint>#/access-packages/<access_package_id>`

1. 通過電子郵件發送或將連結發送給您的外部業務合作夥伴。 他們可以與使用者共用連結以請求訪問包。

## <a name="next-steps"></a>後續步驟

- [請求訪問包](entitlement-management-request-access.md)
- [批准或拒絕訪問請求](entitlement-management-request-approve.md)