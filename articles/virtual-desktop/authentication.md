---
title: Windows 虛擬桌面驗證-Azure
description: 適用于 Windows 虛擬桌面的驗證方法。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 5681228e5da2708912d69f16a4b09a4a93d8bb04
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89500295"
---
# <a name="supported-authentication-methods"></a>支援的驗證方法

在本文中，我們將簡短介紹您可在 Windows 虛擬桌面中使用的驗證類型。

## <a name="session-host-authentication"></a>工作階段主機驗證

Windows 虛擬桌面同時支援 NT LAN Manager (NTLM) ，以及用於工作階段主機驗證的 Kerberos。 不過，若要使用 Kerberos，用戶端必須從網域控制站上執行的金鑰發佈中心 (KDC) 服務取得 Kerberos 安全性票證。 若要取得票證，用戶端必須直接看到網域控制站。 您可以使用您的公司網路來取得直接的看到線。 您也可以使用 VPN 連線來連線到您的公司網路。

以下是目前支援的登入方法：

- Windows 桌面用戶端
    - 使用者名稱和密碼
    - Smartcard
    - Windows Hello
- Windows Store 用戶端
    - 使用者名稱和密碼
- 網頁用戶端
    - 使用者名稱和密碼
- Android
    - 使用者名稱和密碼
- iOS
    - 使用者名稱和密碼
- macOS
    - 使用者名稱和密碼

>[!NOTE]
>智慧卡和 Windows Hello 只能使用 Kerberos 登入。 使用 Kerberos 登入需要網域控制站的可見度。

## <a name="hybrid-identity"></a>混合式身分識別

Windows 虛擬桌面透過 Azure Active Directory (AD) 支援 [混合](../active-directory/hybrid/whatis-hybrid-identity.md) 式身分識別，包括使用 ACTIVE DIRECTORY 同盟服務 (ADFS) 的同盟身分識別。 由於使用者必須可透過 Azure AD 探索，因此 Windows 虛擬桌面不支援使用 ADFS 進行獨立 Active Directory 部署。

## <a name="single-sign-on-sso"></a>單一登入 (SSO)

Windows 虛擬桌面目前不支援 SSO) 的 Active Directory 同盟服務 (ADFS。

避免系統提示您輸入工作階段主機認證的唯一方法，就是將它們儲存在用戶端中。 我們建議您只使用安全裝置來進行此操作，以防止其他使用者存取您的資源。

## <a name="next-steps"></a>後續步驟

想知道有哪些其他方法可以保護您的部署安全嗎？ 查看 [安全性最佳作法](security-guide.md)。
