---
title: Windows 虛擬桌面驗證-Azure
description: 適用于 Windows 虛擬桌面的驗證方法。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/27/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 8328fa9c9e32a22e8e5093aa343f333cd1914ddc
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2020
ms.locfileid: "89038483"
---
# <a name="supported-authentication-methods"></a>支援的驗證方法

在本文中，我們將簡短介紹您可在 Windows 虛擬桌面中使用的驗證類型。

## <a name="session-host-authentication"></a>工作階段主機驗證

Windows 虛擬桌面同時支援 NT LAN Manager (NTLM) ，以及用於工作階段主機驗證的 Kerberos。 不過，若要使用 Kerberos，用戶端必須從網域控制站上執行的金鑰發佈中心 (KDC) 服務取得 Kerberos 安全性票證。 若要取得票證，用戶端必須直接看到網域控制站。 您可以使用您的公司網路來取得直接的看到線。 您也可以使用 VPN 連線來連線到您的公司網路。

以下是目前支援的登入方法：

- Windows 桌面用戶端
    - 使用者名稱和密碼
    - 智慧卡
    - Windows Hello
- Windows Store 用戶端
    - 使用者名稱和密碼
- Web 用戶端
    - 使用者名稱和密碼
- Android
    - 使用者名稱和密碼
- iOS
    - 使用者名稱和密碼
- macOS
    - 使用者名稱和密碼

>[!NOTE]
>智慧卡和 Windows Hello 只能使用 Kerberos 登入。 使用 Kerberos 登入需要網域控制站的可見度。

## <a name="single-sign-on-sso"></a>單一登入 (SSO) 

Windows 虛擬桌面目前並不支援 Active Directory 同盟服務 (ADFS) 進行驗證或 SSO。

避免系統提示您輸入工作階段主機認證的唯一方法，就是將它們儲存在用戶端中。 我們建議您只使用安全裝置來進行此操作，以防止其他使用者存取您的資源。

## <a name="next-steps"></a>後續步驟

想知道有哪些其他方法可以保護您的部署安全嗎？ 查看 [安全性最佳作法](security-guide.md)。
