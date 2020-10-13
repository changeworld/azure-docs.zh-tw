---
title: 在權利管理中隱藏或刪除存取套件-Azure AD
description: 瞭解如何在 Azure Active Directory 權利管理中隱藏或刪除存取套件。
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
ms.openlocfilehash: 22aa97c6e627c2072636ca2e079877ff0f608b68
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87798795"
---
# <a name="hide-or-delete-an-access-package-in-azure-ad-entitlement-management"></a>在 Azure AD 權利管理中隱藏或刪除存取套件

存取套件預設為可探索。 這表示，如果原則允許使用者要求存取套件，則會自動看到我的存取權入口網站中列出的存取套件。 不過，您可以變更 **隱藏** 設定，讓存取套件不會列在使用者的我的存取權入口網站中。

本文說明如何隱藏或刪除存取套件。

## <a name="change-the-hidden-setting"></a>變更隱藏的設定

請遵循下列步驟來變更存取套件的 **隱藏** 設定。

**必要角色：** 全域管理員、使用者系統管理員、目錄擁有者或存取套件管理員

1. 在 Azure 入口網站中按一下 [Azure Active Directory]  ，然後按一下 [身分識別治理]  。

1. 在左側功能表中，按一下 [ **存取套件** ]，然後開啟存取套件。

1. 在 [總覽] 頁面上，按一下 [ **編輯**]。

1. 設定 **隱藏** 的設定。

    如果設定為 [ **否**]，存取套件將會列在使用者的我的存取權入口網站中。

    如果設定為 **[是]**，則存取套件將不會列在使用者的我的存取權入口網站中。 使用者可以查看存取套件的唯一方式，是他們是否擁有存取套件的直接 **我的存取權入口網站連結** 。 如需詳細資訊，請參閱 [共用連結以要求存取套件](entitlement-management-access-package-settings.md)。

## <a name="delete-an-access-package"></a>刪除存取套件

只有在沒有任何作用中的使用者指派時，才能刪除存取套件。 請遵循下列步驟來刪除存取套件。

**必要角色：** 全域管理員、使用者系統管理員、目錄擁有者或存取套件管理員

1. 在 Azure 入口網站中按一下 [Azure Active Directory]  ，然後按一下 [身分識別治理]  。

1. 在左側功能表中，按一下 [ **存取套件** ]，然後開啟存取套件。

1. 在左側功能表中，按一下 [ **指派** ]，然後移除所有使用者的存取權。

1. 在左側功能表中，按一下 **[總覽** ]，然後按一下 [ **刪除**]。

1. 在出現的 [刪除] 訊息中，按一下 [ **是]**。

## <a name="next-steps"></a>後續步驟

- [查看、新增及移除存取套件的指派](entitlement-management-access-package-assignments.md)
- [檢視報告和記錄](entitlement-management-reports.md)
