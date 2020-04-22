---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 04/07/2020
ms.author: mimart
ms.openlocfilehash: 31a6d116ae1afce9afdd3786747490980963d823
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678056"
---
## <a name="register-an-azure-ad-app"></a>註冊 Azure AD 應用程式

若要讓特定 Azure AD 組織的使用者登入，您需要在組織 Azure AD 租用戶內註冊應用程式。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 確保使用的目錄包含組織 Azure AD 租戶(例如,contoso.com)。 選擇頂部功能表中的**目錄 + 訂閱篩選器**,然後選擇包含 Azure AD 租戶的目錄。
1. 選擇 Azure 入口網站左上角的 [所有服務]****，然後搜尋並選取 [應用程式註冊]****。
1. 選取 [新增註冊]  。
1. 輸入應用程式**的名稱**。 例如： `Azure AD B2C App` 。
1. **僅接受此組織目錄中此組織目錄中**的默認選擇帳戶。
1. 對於**重定向 URI**,接受**Web**的值,並在所有小寫字母中輸入`your-B2C-tenant-name`以下 URL,其中 替換為 Azure AD B2C 租戶的名稱。

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    例如： `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp` 。

1. 選取 [註冊]  。 記錄 [應用程式 (用戶端) 識別碼]****，以便在稍後的步驟中使用。
1. 選擇**憑證&機密**,然後選擇 **「新用戶端機密**」。
1. 輸入機密**的描述**,選擇過期,然後選擇 **"添加**"。 記錄機密**的值**,以便在後面的步驟中使用。

### <a name="configuring-optional-claims"></a>設定選擇性宣告

如果要從 Azure `family_name` `given_name`AD 獲取和聲明,可以在 Azure 門戶 UI 或應用程式清單中為應用程式配置可選聲明。 有關詳細資訊,請參閱[如何向 Azure AD 應用程式提供可選聲明](/active-directory/develop/active-directory-optional-claims.md)。

1. 登入 [Azure 入口網站](https://portal.azure.com)。 搜尋並選取 [Azure Active Directory]  。
1. 在 **"管理'** 部分中,選擇**套用註冊**。
1. 選擇要在清單中為其配置可選聲明的應用程式。
1. 在 **'管理**' 部份中, 選擇**權杖設定**。
1. 選擇 **「新增可選聲明**」。
1. 此**類型**, 請選擇**ID**。
1. 選擇要新增的選擇的聲明,`family_name``given_name`與 。
1. 按一下 [新增]  。