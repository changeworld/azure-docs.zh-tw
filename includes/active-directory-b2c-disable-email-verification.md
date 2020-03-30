---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 03/11/2020
ms.author: mimart
ms.openlocfilehash: 4e03d076fc9ed7d87326a4c8b4f8bf6c0b21c750
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79126727"
---
## <a name="disable-email-verification"></a>停用電子郵件驗證

預設情況下，Azure 活動目錄 B2C（Azure AD B2C）驗證客戶本地帳戶的電子郵件地址（使用電子郵件地址或使用者名註冊的使用者的帳戶）。 Azure AD B2C 通過要求客戶在註冊過程中驗證這些電子郵件地址來確保有效的電子郵件地址。 它還可以防止惡意參與者使用自動進程在應用程式中生成欺詐帳戶。

一些應用程式開發人員喜歡在註冊過程中跳過電子郵件驗證，而是讓客戶稍後驗證其電子郵件地址。 為支援此方式，您可以將 Azure AD B2C 設定為停用電子郵件驗證。 這樣做可以創建更順暢的註冊流程，使開發人員能夠靈活地將驗證其電子郵件地址的客戶與尚未驗證其電子郵件地址的客戶區分開來。

> [!WARNING]
> 在註冊程序期間停用電子郵件驗證可能會導致收到垃圾郵件。 如果禁用預設的 Azure AD B2C 提供的電子郵件驗證，我們建議您實施替換驗證系統。
