---
title: 使用 Azure AD 應用程式 Proxy 設定應用程式的單一登入 | Microsoft Docs
description: 在 Azure 入口網站中使用 Azure AD 應用程式 Proxy，為您已發佈的內部部署應用程式啟動單一登入。
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 11/12/2018
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: b0259a8d9fcb4c9c513ab2c31103c9a8488e90ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025736"
---
# <a name="password-vaulting-for-single-sign-on-with-application-proxy"></a>使用應用程式 Proxy 進行單一登入的密碼保存庫

Azure Active Directory 應用程式 Proxy 可發佈內部部署應用程式，讓遠端員工也可以安全地存取它們，進而幫助您改進生產力。 在 Azure 入口網站中，您也可以設定這些應用程式的單一登入 (SSO)。 您的使用者只需要向 Azure AD 驗證，便可以存取您的企業應用程式，而不必再次登入。

應用程式 Proxy 支援數個[單一登入模式](what-is-single-sign-on.md#choosing-a-single-sign-on-method)。 密碼型登入適用於使用使用者名稱/密碼組合進行驗證的應用程式。 當您設定應用程式的密碼型登入您時，您的使用者必須登入一次內部部署應用程式。 之後，Azure Active Directory 會儲存登入資訊，並且會在您的使用者從遠端存取時，自動將登入資訊提供給應用程式。

您應該已經使用應用程式 Proxy 發行並測試您的應用程式。 如果還沒，請依照[使用 Azure AD 應用程式 Proxy 發佈應用程式](application-proxy-add-on-premises-application.md)的步驟操作，然後回到這裡。

## <a name="set-up-password-vaulting-for-your-application"></a>為應用程式設定密碼儲存庫存

1. 以管理員身份登錄到[Azure 門戶](https://portal.azure.com)。
1. 選擇**Azure 活動目錄** > **企業應用程式** > **所有應用程式**。
1. 從清單中選取您要設定 SSO 的應用程式。  
1. 選取 [應用程式 Proxy]****。 
1. 將**預身份驗證類型**更改為 **"傳遞"，** 然後選擇 **"保存**"。 稍後，您可以再次切換回**Azure 活動目錄**類型！ 
1. 選擇**單一登入**。

   ![從應用的概述頁面選擇單一登入](./media/application-proxy-configure-single-sign-on-password-vaulting/select-sso.png)

1. 在 SSO 模式中，選擇 [密碼型登入]****。
1. 在登入 URL 輸入頁面 URL，使用者將在該頁面輸入其使用者名稱和密碼，以登入公司網路外部的應用程式。 這可能是您透過應用程式 Proxy 發佈應用程式時建立的外部 URL。

   ![選擇密碼型登入並輸入您的 URL](./media/application-proxy-configure-single-sign-on-password-vaulting/password-sso.png)

1. 選取 [儲存]****。
1. 選取 [應用程式 Proxy]****。 
1. 將**預身份驗證類型**更改為**Azure 活動目錄**，然後選擇 **"保存**"。 
1. 選擇**使用者和組**。
1. 通過選擇 **"添加使用者**"將使用者分配給應用程式。 
1. 如果要預定義使用者的憑據，請選中使用者名前面的核取方塊，然後選擇 **"更新憑據**"。
1. 選擇**Azure 活動目錄** > **應用註冊** > **所有應用程式**。
1. 從清單中選擇使用密碼 SSO 配置的應用。
1. 選取 [建立品牌]****。 
1. 使用密碼 SSO 頁**中的"登錄 URL"** 更新**主頁 URL，** 然後選擇 **"保存**"。  



<!-- Need to repro?
7. The page should tell you that a sign-in form was successfully detected at the provided URL. If it doesn't, select **Configure [your app name] Password Single Sign-on Settings** and choose **Manually detect sign-in fields**. Follow the instructions to point out where the sign-in credentials go. 
-->

## <a name="test-your-app"></a>測試應用程式

轉到"我的應用"門戶。 使用憑據（或使用存取權限設置的測試帳戶的憑據）登錄。 成功登錄後，按一下應用的圖示。 這可能會觸發安裝"我的應用安全登錄瀏覽器"擴展。 如果使用者具有預定義的憑據，則應用的身份驗證應自動進行，否則必須首次指定使用者名或密碼。 

## <a name="next-steps"></a>後續步驟

- 閱讀其他實作[單一登入](what-is-single-sign-on.md)的做法
- 深入了解[使用 Azure AD 應用程式 Proxy 遠端存取應用程式的安全性考量](application-proxy-security.md)
