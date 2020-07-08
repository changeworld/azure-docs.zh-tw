---
title: 變更 Azure AD 權利管理中存取套件的生命週期設定-Azure Active Directory
description: 瞭解如何在 Azure Active Directory 權利管理中變更存取套件的生命週期設定。
services: active-directory
documentationCenter: ''
author: msaburnley
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
ms.openlocfilehash: 538ed31c1f3bbca8c2151a6ac0d2a3ad561bc6a7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85078775"
---
# <a name="change-lifecycle-settings-for-an-access-package-in-azure-ad-entitlement-management"></a>在 Azure AD 權利管理中變更存取套件的生命週期設定

身為存取套件管理員，您可以隨時編輯現有的原則來變更存取套件的生命週期設定。 如果您變更原則的到期日，已處於 [待核准] 或 [已核准] 狀態之要求的到期日將不會變更。

本文說明如何變更現有存取套件的生命週期設定。

## <a name="open-lifecycle-settings"></a>開啟生命週期設定

若要變更存取套件的生命週期設定，您需要開啟對應的原則。 請遵循下列步驟來開啟存取套件的生命週期設定。

**必要角色：** 全域管理員、使用者系統管理員、目錄擁有者或存取套件管理員

1. 在 Azure 入口網站中按一下 [Azure Active Directory]  ，然後按一下 [身分識別治理]  。

1. 在左側功能表中，按一下 [**存取套件**]，然後開啟存取封裝。

1. 按一下 [**原則**]，然後按一下具有您要編輯之生命週期設定的原則。

    [原則詳細資料] 窗格會在頁面底部開啟。

    ![存取套件-原則詳細資料窗格](./media/entitlement-management-shared/policy-details.png)

1. 按一下 [**編輯**] 以編輯原則。

    ![存取套件-編輯原則](./media/entitlement-management-shared/policy-edit.png)

1. 按一下 [**生命週期**] 索引標籤以開啟 [生命週期設定]。

[!INCLUDE [Entitlement management lifecycle policy](../../../includes/active-directory-entitlement-management-lifecycle-policy.md)]

## <a name="next-steps"></a>後續步驟

- [變更存取套件的要求和核准設定](entitlement-management-access-package-request-policy.md)