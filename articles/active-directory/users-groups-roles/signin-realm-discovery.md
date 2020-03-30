---
title: 登錄期間的使用者名查找 - Azure 活動目錄 |微軟文檔
description: 螢幕消息如何反映 Azure 活動目錄中登錄期間的使用者名查找
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
ms.openlocfilehash: c8b6a65a964016f702fcf75aa4cbdab33a952e3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74024257"
---
# <a name="home-realm-discovery-for-azure-active-directory-sign-in-pages"></a>Azure 活動目錄登錄頁的家庭域發現

我們正在更改 Azure 活動目錄 （Azure AD） 登錄行為，以便為新身份驗證方法騰出空間並提高可用性。 在登錄期間，Azure AD 確定使用者需要身份驗證的位置。 Azure AD 通過讀取登錄頁上輸入的使用者名的組織和使用者設置來做出明智的決策。 這是邁向無密碼未來的一個步驟，它啟用了 FIDO 2.0 等其他憑據。

## <a name="home-realm-discovery-behavior"></a>主頁 領域發現行為

從歷史上看，主域發現受登錄時提供的域或某些遺留應用程式的家庭域發現策略控制。 例如，在我們的發現行為中，Azure 活動目錄使用者可能會鍵入錯誤的使用者名，但仍會到達其組織的憑據集合螢幕。 當使用者正確提供組織的功能變數名稱"contoso.com"時，將發生這種情況。 此行為不允許細微性自訂單個使用者的體驗。

為了支援更廣泛的憑據並提高可用性，Azure Active Directory 在登錄過程中的使用者名查找行為現已更新。 新行為根據登錄頁上輸入的使用者名讀取租戶和使用者級別設置，從而做出明智的決策。 為此，Azure 活動目錄將檢查在其指定域中輸入的登錄頁上輸入的使用者名是否存在，或重定向使用者以提供其憑據。

這項工作的另一個好處是改進了錯誤訊息。 下面是一些在登錄到僅支援 Azure 活動目錄使用者的應用程式時改進的錯誤訊息傳遞的一些示例。

- 使用者名鍵入錯誤或使用者名尚未同步到 Azure AD：
  
    ![使用者名鍵入錯誤或未找到](./media/signin-realm-discovery/typo-username.png)
  
- 功能變數名稱鍵入錯誤：
  
    ![功能變數名稱鍵入錯誤或未找到](./media/signin-realm-discovery/typo-domain.png)
  
- 使用者嘗試使用已知的消費者域登錄：
  
    ![使用已知消費者域登錄](./media/signin-realm-discovery/consumer-domain.png)
  
- 密碼鍵入錯誤，但使用者名準確：  
  
    ![密碼輸入錯誤，輸入良好的使用者名](./media/signin-realm-discovery/incorrect-password.png)
  
> [!IMPORTANT]
> 此功能可能會影響聯合域，這些域依賴于舊的域級主域發現來強制聯合。 有關何時添加聯合域支援的更新，請參閱[Microsoft 365 服務登錄期間發現主頁領域](https://azure.microsoft.com/updates/signin-hrd/)。 同時，某些組織已訓練其員工使用 Azure 活動目錄中不存在但包含適當功能變數名稱的使用者名登錄，因為功能變數名稱將當前使用者路由到其組織的域終結點。 新的登錄行為不允許這樣做。 將通知使用者更正使用者名，並且不允許使用者使用 Azure 活動目錄中不存在的使用者名登錄。
>
> 如果您或您的組織有依賴于舊行為的做法，組織管理員必須更新員工登錄和身份驗證文檔，並訓練員工使用其 Azure 活動目錄使用者名登錄。
  
如果您對新行為有顧慮，請將您的備註保留在本文的 **"回饋"** 部分。  

## <a name="next-steps"></a>後續步驟

[自訂登錄品牌](../fundamentals/add-custom-domain.md)
