---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 03/11/2020
ms.author: mimart
ms.openlocfilehash: 4e03d076fc9ed7d87326a4c8b4f8bf6c0b21c750
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/11/2020
ms.locfileid: "79126727"
---
## <a name="disable-email-verification"></a>停用電子郵件驗證

根據預設，Azure Active Directory B2C （Azure AD B2C）會驗證您客戶的本機帳戶電子郵件地址（使用電子郵件地址或使用者名稱註冊之使用者的帳戶）。 Azure AD B2C 藉由要求客戶在註冊程式期間進行驗證，來確保有效的電子郵件地址。 它也可防止惡意執行者在您的應用程式中使用自動化進程來產生詐騙帳戶。

有些應用程式開發人員偏好在註冊過程中略過電子郵件驗證，而改為讓客戶在稍後驗證其電子郵件地址。 為支援此方式，您可以將 Azure AD B2C 設定為停用電子郵件驗證。 這麼做會建立更流暢的註冊程式，讓開發人員能夠彈性地區分已驗證其電子郵件地址的客戶，而不是。

> [!WARNING]
> 在註冊程序期間停用電子郵件驗證可能會導致收到垃圾郵件。 如果您停用預設 Azure AD B2C 提供的電子郵件驗證，建議您執行更換驗證系統。
