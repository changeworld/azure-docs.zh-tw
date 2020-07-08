---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 04/07/2020
ms.author: mimart
ms.openlocfilehash: 818f33abfdb0655d96c0a8873a43903ee972b3bf
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "82925402"
---
## <a name="register-an-azure-ad-app"></a>註冊 Azure AD 應用程式

若要讓特定 Azure AD 組織的使用者登入，您需要在組織 Azure AD 租用戶內註冊應用程式。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 請確定您使用的是包含組織 Azure AD 租使用者的目錄（例如 contoso.com）。 在頂端功能表中選取 [**目錄 + 訂**用帳戶] 篩選，然後選擇包含您 Azure AD 租使用者的目錄。
1. 選擇 Azure 入口網站左上角的 [所有服務]，然後搜尋並選取 [應用程式註冊]。
1. 選取 [新增註冊]。
1. 輸入應用程式的**名稱**。 例如： `Azure AD B2C App` 。
1. 僅針對此應用程式，接受**此組織目錄中帳戶**的預設選項。
1. 針對 [重新**導向 URI**]，接受 [ **Web**] 的值，並以小寫字母輸入下列 URL，其中 `your-B2C-tenant-name` 會以您的 Azure AD B2C 租使用者名稱取代。

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    例如： `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp` 。

1. 選取 [註冊]。 記錄 [應用程式 (用戶端) 識別碼]，以便在稍後的步驟中使用。
1. 選取 [**憑證 & 密碼**]，然後選取 [**新增用戶端密碼**]。
1. 輸入密碼的**描述**，選取到期日，然後選取 [**新增**]。 記錄密碼的**值**，以便在稍後的步驟中使用。

### <a name="configuring-optional-claims"></a>設定選擇性宣告

如果您想要從 Azure AD 取得 `family_name` 和 `given_name` 宣告，您可以在 Azure 入口網站 UI 或應用程式資訊清單中設定應用程式的選擇性宣告。 如需詳細資訊，請參閱[如何為 Azure AD 應用程式提供選擇性宣告](/azure/active-directory/develop/active-directory-optional-claims)。

1. 登入 [Azure 入口網站](https://portal.azure.com)。 搜尋並選取 [Azure Active Directory]。
1. 從 [管理] 區段中，選取 [應用程式註冊]。
1. 從清單中，選取您要設定選擇性宣告的應用程式。
1. 從 [管理] 區段中，選取 [權杖設定]。
1. 選取 [新增選擇性宣告]。
1. 針對 [**權杖類型**]，選取 [ **ID**]。
1. 選取要加入的選擇性宣告， `family_name` 以及 `given_name` 。
1. 按一下 **[新增]** 。