---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 03/11/2020
ms.author: mimart
ms.openlocfilehash: 4e03d076fc9ed7d87326a4c8b4f8bf6c0b21c750
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "79126727"
---
## <a name="disable-email-verification"></a>停用電子郵件驗證

根據預設，Azure Active Directory B2C (Azure AD B2C) 會針對使用電子郵件地址或使用者名稱 (註冊的使用者，驗證客戶的本機帳戶電子郵件地址) 帳戶。 Azure AD B2C 在註冊程式期間要求客戶驗證電子郵件地址，以確保有效的電子郵件地址。 它也可防止惡意執行者在您的應用程式中使用自動化進程產生詐騙帳戶。

有些應用程式開發人員偏好在註冊程式期間略過電子郵件驗證，而改為讓客戶在稍後驗證其電子郵件地址。 為支援此方式，您可以將 Azure AD B2C 設定為停用電子郵件驗證。 這樣做會建立更流暢的註冊程式，並讓開發人員有彈性地區分已驗證其電子郵件地址的客戶，而不是來自不是的客戶。

> [!WARNING]
> 在註冊程序期間停用電子郵件驗證可能會導致收到垃圾郵件。 如果您停用預設的 Azure AD B2C 提供的電子郵件驗證，建議您執行取代驗證系統。
