---
title: 在授權管理中隱藏或刪除訪問包 - Azure AD
description: 瞭解如何在 Azure 活動目錄授權管理中隱藏或刪除訪問包。
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
ms.openlocfilehash: cf410db5a483fc4f5d9efc1a6bd5c6202694ef4c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261992"
---
# <a name="hide-or-delete-an-access-package-in-azure-ad-entitlement-management"></a>在 Azure AD 授權管理中隱藏或刪除訪問包

預設情況下，可發現訪問包。 這意味著，如果策略允許使用者請求訪問包，他們將自動看到其"我的訪問"門戶中列出的訪問包。 但是，您可以更改 **"隱藏"** 設置，以便訪問包不會列在使用者的"我的訪問"門戶中。

本文介紹如何隱藏或刪除訪問包。

## <a name="change-the-hidden-setting"></a>更改隱藏設置

按照以下步驟更改訪問包的**隱藏**設置。

**先決條件角色：** 全域管理員、使用者管理員、目錄擁有者或訪問包管理器

1. 在 Azure 入口網站中按一下 [Azure Active Directory]****，然後按一下 [身分識別治理]****。

1. 在左側功能表中，按一下 **"訪問包**"，然後打開訪問包。

1. 在"概述"頁上，按一下"**編輯**"。

1. 設置 **"隱藏"** 設置。

    如果設置為 **"否**"，訪問包將列在使用者的"我的訪問"門戶中。

    如果設置為 **"是**"，則訪問包將不會在使用者的"我的訪問"門戶中列出。 使用者查看訪問包的唯一方法是，如果他們具有直接**的"我的訪問"門戶連結到**訪問包。 有關詳細資訊，請參閱[共用連結以請求訪問包](entitlement-management-access-package-settings.md)。

## <a name="delete-an-access-package"></a>刪除訪問包

僅當訪問包沒有活動使用者分配時，才能刪除它。 按照以下步驟刪除訪問包。

**先決條件角色：** 全域管理員、使用者管理員、目錄擁有者或訪問包管理器

1. 在 Azure 入口網站中按一下 [Azure Active Directory]****，然後按一下 [身分識別治理]****。

1. 在左側功能表中，按一下 **"訪問包**"，然後打開訪問包。

1. 在左側功能表中，按一下"**分配"** 並刪除所有使用者的存取權限。

1. 在左側功能表中，按一下 **"概述"，** 然後按一下"**刪除**"。

1. 在顯示的刪除郵件中，按一下"**是**"。

## <a name="next-steps"></a>後續步驟

- [查看、添加和刪除訪問包的分配](entitlement-management-access-package-assignments.md)
- [檢視報告和記錄](entitlement-management-reports.md)
