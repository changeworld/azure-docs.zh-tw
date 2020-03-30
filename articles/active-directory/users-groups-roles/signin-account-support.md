---
title: 我的 Azure AD 登錄頁是否接受 Microsoft 帳戶 |微軟文檔
description: 螢幕消息如何反映登錄期間的使用者名查找
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: kexia
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 221ab7c50a84650f1b2adf3fdb2b284365795f42
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74024292"
---
# <a name="sign-in-options-for-microsoft-accounts-in-azure-active-directory"></a>Azure 活動目錄中 Microsoft 帳戶的登錄選項

Azure 活動目錄 （Azure AD） 的 Microsoft 365 登錄頁支援工作或學校帳戶和 Microsoft 帳戶，但根據使用者的情況，它可以是一個或另一個或兩者兼而有之。 例如，Azure AD 登錄頁支援：

* 接受來自這兩種類型的帳戶的登錄的應用
* 接受客人的組織

## <a name="identification"></a>識別
您可以通過查看使用者名欄位中的提示文本來判斷組織使用的登錄頁是否支援 Microsoft 帳戶。 如果提示文本顯示"電子郵件、電話或 Skype"，登錄頁面將支援 Microsoft 帳戶。

![帳戶登錄頁之間的差異](./media/signin-account-support/ui-prompt.png)

[其他登錄選項僅適用于個人 Microsoft 帳戶](https://azure.microsoft.com/updates/microsoft-account-signin-options/ )，但不能用於登錄到工作或學校帳戶資源。

## <a name="next-steps"></a>後續步驟

[自訂登錄品牌](../fundamentals/add-custom-domain.md)