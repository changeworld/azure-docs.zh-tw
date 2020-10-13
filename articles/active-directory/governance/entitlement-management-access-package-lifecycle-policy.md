---
title: 在 Azure AD 權利管理中變更存取套件的生命週期設定-Azure Active Directory
description: 瞭解如何在 Azure Active Directory 權利管理中變更存取套件的要求者資訊 & 生命週期設定。
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
ms.openlocfilehash: 4fd119238476b842a4dba1efeec86544ca263920
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90980128"
---
# <a name="change-lifecycle-settings-for-an-access-package-in-azure-ad-entitlement-management"></a>在 Azure AD 權利管理中變更存取套件的生命週期設定

您可以藉由編輯現有的原則，隨時變更存取套件的生命週期設定，作為存取套件管理員。 如果您變更原則的到期日，則已處於 [等待核准] 或 [已核准] 狀態之要求的到期日將不會變更。

本文說明如何變更現有存取套件的生命週期設定。

## <a name="open-requestor-information"></a>開啟要求者資訊
為了確保使用者具有存取套件的正確存取權，您可以設定自訂問題，要求使用者要求存取特定存取套件。 設定選項包括：當地語系化、必要/選用，以及文字/多重選取的回應格式。 要求者會在要求套件時看到問題，而核准者會看到問題的答案，以協助他們做出決策。 使用下列步驟來設定存取套件中的問題：

## <a name="open-lifecycle-settings"></a>開啟生命週期設定

若要變更存取套件的生命週期設定，您需要開啟對應的原則。 請遵循下列步驟來開啟存取套件的生命週期設定。

**必要角色：** 全域管理員、使用者系統管理員、目錄擁有者或存取套件管理員

1. 在 Azure 入口網站中按一下 [Azure Active Directory]  ，然後按一下 [身分識別治理]  。

1. 在左側功能表中，按一下 [ **存取套件** ]，然後開啟存取套件。

1. 按一下 [ **原則** ]，然後按一下具有您想要編輯之生命週期設定的原則。

    [原則詳細資料] 窗格會在頁面底部開啟。

    ![存取套件-原則詳細資料窗格](./media/entitlement-management-shared/policy-details.png)

1. 按一下 [ **編輯** ] 以編輯原則。

    ![存取套件-編輯原則](./media/entitlement-management-shared/policy-edit.png)

1. 按一下 [ **生命週期** ] 索引標籤以開啟生命週期設定。

[!INCLUDE [Entitlement management lifecycle policy](../../../includes/active-directory-entitlement-management-lifecycle-policy.md)]

## <a name="next-steps"></a>後續步驟

- [變更存取套件的要求和核准設定](entitlement-management-access-package-request-policy.md)