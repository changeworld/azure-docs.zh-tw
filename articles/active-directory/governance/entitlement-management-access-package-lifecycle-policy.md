---
title: 更改 Azure AD 授權管理中訪問包的生命週期設置 - Azure 活動目錄
description: 瞭解如何在 Azure 活動目錄授權管理中更改訪問包的生命週期設置。
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
ms.openlocfilehash: 959d85f496a4a573a969bf736aba137d5b86154a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261979"
---
# <a name="change-lifecycle-settings-for-an-access-package-in-azure-ad-entitlement-management"></a>更改 Azure AD 授權管理中訪問包的生命週期設置

作為訪問包管理器，您可以隨時通過編輯現有策略來更改訪問包的生命週期設置。 如果更改策略的到期日期，則已處於待審批或已批准狀態的請求的到期日期將不會更改。

本文介紹如何更改現有訪問包的生命週期設置。

## <a name="open-lifecycle-settings"></a>開放生命週期設置

要更改訪問包的生命週期設置，您需要打開相應的策略。 按照以下步驟打開訪問包的生命週期設置。

**先決條件角色：** 全域管理員、使用者管理員、目錄擁有者或訪問包管理器

1. 在 Azure 入口網站中按一下 [Azure Active Directory]****，然後按一下 [身分識別治理]****。

1. 在左側功能表中，按一下 **"訪問包**"，然後打開訪問包。

1. 按一下 **"策略"，** 然後按一下具有要編輯的生命週期設置的策略。

    "策略詳細資訊"窗格將在頁面底部打開。

    ![訪問包 - 策略詳細資訊窗格](./media/entitlement-management-shared/policy-details.png)

1. 按一下 **"編輯"** 以編輯策略。

    ![訪問包 - 編輯策略](./media/entitlement-management-shared/policy-edit.png)

1. 按一下 **"生命週期**"選項卡以打開生命週期設置。

[!INCLUDE [Entitlement management lifecycle policy](../../../includes/active-directory-entitlement-management-lifecycle-policy.md)]

## <a name="next-steps"></a>後續步驟

- [更改訪問包的請求和審批設置](entitlement-management-access-package-request-policy.md)