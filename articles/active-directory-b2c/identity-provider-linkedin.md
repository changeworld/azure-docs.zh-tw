---
title: 使用LinkedIn帳戶設置註冊和登錄
titleSuffix: Azure AD B2C
description: 使用 Azure Active Directory B2C，讓具有 LinkedIn 帳戶的客戶得以註冊和登入您的應用程式。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d32eb80ffe296d86164a6d27a1b7a28181357243
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188095"
---
# <a name="set-up-sign-up-and-sign-in-with-a-linkedin-account-using-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 設定註冊，並以 LinkedIn 帳戶登入

## <a name="create-a-linkedin-application"></a>建立 LinkedIn 應用程式

要在 Azure 活動目錄 B2C（Azure AD B2C） 中使用LinkedIn帳戶作為[標識提供程式](authorization-code-flow.md)，需要在租戶中創建表示它的應用程式。 如果您還沒有LinkedIn帳戶，則可以在 註冊[https://www.linkedin.com/](https://www.linkedin.com/)。

1. 使用您的 LinkedIn 帳戶認證登入 [LinkedIn 開發人員網站](https://www.developer.linkedin.com/)。
1. 選取 [我的應用程式]****，然後按一下 [建立應用程式]****。
1. 請輸入 [公司名稱]****[應用程式名稱]****、[應用程式描述]****、[應用程式標誌]****、[應用程式使用]****、[網站 URL]****、[公司電子郵件]****，以及 [公司電話]****。
1. 同意 **LinkedIn API 使用條款**，然後按一下 [提交]****。
1. 複製**用戶端 ID**和**用戶端機密的值**。 您可以在**身份驗證金鑰**下找到它們。 您必須使用這兩個值，將 LinkedIn 設為租用戶中的身分識別提供者。 **用戶端密碼**是重要的安全性認證。
1. 請在 [授權重新導向 URL]**** 中輸入 `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`。 以您的租用戶名稱取代 `your-tenant-name`。 即使租用戶在 Azure AD B2C 中是使用大寫字母來定義的，您還是需要在輸入租用戶名稱時，全部使用小寫字母。 選取 [新增]****，然後按一下 [更新]****。

## <a name="configure-a-linkedin-account-as-an-identity-provider"></a>將 LinkedIn 帳戶設為識別提供者

1. 以 Azure AD B2C 租戶的全域管理員身份登錄到[Azure 門戶](https://portal.azure.com/)。
1. 選取頂端功能表中的 [目錄 + 訂用帳戶]**** 篩選，然後選擇包含您租用戶的目錄，以確定您使用的是包含 Azure AD B2C 租用戶的目錄。
1. 選擇 Azure 入口網站左上角的 [所有服務]****，搜尋並選取 [Azure AD B2C]****。
1. 選擇**標識提供程式**，然後選擇**LinkedIn**。
1. 輸入**名稱**。 例如 *，LinkedIn*。
1. 對於**用戶端 ID，** 輸入您之前創建的LinkedIn應用程式的用戶端 ID。
1. 對於**客戶機密**，輸入您記錄的客戶金鑰。
1. 選取 [儲存]****。

## <a name="migration-from-v10-to-v20"></a>從 v1.0 遷移到 v2.0

LinkedIn最近[將其 API 從 v1.0 更新為 v2.0](https://engineering.linkedin.com/blog/2018/12/developer-program-updates)。 作為遷移的一部分，Azure AD B2C 只能在註冊期間獲取LinkedIn使用者的全名。 如果電子郵件地址是在註冊過程中收集的屬性之一，則使用者必須手動輸入電子郵件地址並對其進行驗證。
