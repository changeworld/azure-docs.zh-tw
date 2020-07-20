---
title: 我的 Azure AD 登入頁面是否接受 Microsoft 帳戶 | Microsoft Docs
description: 登入期間螢幕上的訊息如何反映使用者名稱查閱
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: overview
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: kexia
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 81ba7f77bb3396f49c3cf41d2266567aa48a8257
ms.sourcegitcommit: c4ad4ba9c9aaed81dfab9ca2cc744930abd91298
ms.contentlocale: zh-TW
ms.lasthandoff: 06/12/2020
ms.locfileid: "84731353"
---
# <a name="sign-in-options-for-microsoft-accounts-in-azure-active-directory"></a>Azure Active Directory 中 Microsoft 帳戶的登入選項

Azure Active Directory (Azure AD) 的 Microsoft 365 登入頁面支援公司或學校帳戶和 Microsoft 帳戶，但視使用者的情況而定，可以是其中之一或兩者。 例如，Azure AD 登入頁面支援：

* 接受這兩種帳戶類型登入的應用程式
* 接受來賓的組織

## <a name="identification"></a>識別
您可藉由查看 [使用者名稱] 欄位中的提示文字，判斷貴組織使用的登入頁面是否支援 Microsoft 帳戶。 如果提示文字顯示「電子郵件、電話或 Skype」，則登入頁面支援 Microsoft 帳戶。

![帳戶登入頁面之間的差異](./media/signin-account-support/ui-prompt.png)

[其他登入選項僅適用於個人 Microsoft 帳戶](https://azure.microsoft.com/updates/microsoft-account-signin-options/ )，但無法用於登入公司或學校帳戶資源。

## <a name="next-steps"></a>後續步驟

[自訂您的登入品牌](../fundamentals/add-custom-domain.md)