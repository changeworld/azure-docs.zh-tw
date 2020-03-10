---
title: 在 權利管理-Azure AD 中隱藏或刪除存取套件
description: 瞭解如何在 Azure Active Directory 權利管理中隱藏或刪除存取套件。
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
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78376674"
---
# <a name="hide-or-delete-an-access-package-in-azure-ad-entitlement-management"></a>在 Azure AD 權利管理中隱藏或刪除存取套件

根據預設，存取封裝是可探索的。 這表示如果原則允許使用者要求存取套件，他們會自動看到其我的存取權入口網站中所列的存取套件。 不過，您可以變更**隱藏**的設定，讓存取套件不會列在使用者的我的存取權入口網站中。

本文說明如何隱藏或刪除存取封裝。

## <a name="change-the-hidden-setting"></a>變更隱藏的設定

請遵循下列步驟來變更存取套件的**隱藏**設定。

**先決條件角色：** 全域管理員、使用者系統管理員、目錄擁有者或存取套件管理員

1. 在 Azure 入口網站中按一下 [Azure Active Directory]，然後按一下 [身分識別治理]。

1. 在左側功能表中，按一下 [**存取套件**]，然後開啟存取封裝。

1. 在 [總覽] 頁面上，按一下 [**編輯**]。

1. 設定**隱藏**的設定。

    如果設定為 [**否**]，存取套件將會列在使用者的我的存取權入口網站中。

    如果設定為 **[是]** ，存取套件就不會列在使用者的我的存取權入口網站中。 使用者可以查看存取套件的唯一方式，就是他們擁有存取套件的直接**我的存取權入口網站連結**。 如需詳細資訊，請參閱[共用連結以要求存取封裝](entitlement-management-access-package-settings.md)。

## <a name="delete-an-access-package"></a>刪除存取套件

只有在沒有作用中的使用者指派時，才能刪除存取封裝。 請遵循下列步驟來刪除存取封裝。

**先決條件角色：** 全域管理員、使用者系統管理員、目錄擁有者或存取套件管理員

1. 在 Azure 入口網站中按一下 [Azure Active Directory]，然後按一下 [身分識別治理]。

1. 在左側功能表中，按一下 [**存取套件**]，然後開啟存取封裝。

1. 在左側功能表中，按一下 [**指派**]，並移除 [所有使用者的存取權]。

1. 在左側功能表中，按一下 **[總覽**]，然後按一下 [**刪除**]。

1. 在出現的 [刪除] 訊息中，按一下 [**是]** 。

## <a name="next-steps"></a>後續步驟

- [查看、新增和移除存取套件的指派](entitlement-management-access-package-assignments.md)
- [查看報表和記錄](entitlement-management-reports.md)
