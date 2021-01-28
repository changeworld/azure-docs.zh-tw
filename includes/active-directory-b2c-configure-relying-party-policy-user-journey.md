---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 01/27/2021
ms.author: mimart
ms.openlocfilehash: 73216b1b089444c1dc92bbe73ed07895de3711b2
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98951511"
---
## <a name="configure-the-relying-party-policy"></a>設定信賴憑證者原則

信賴憑證者原則（例如 [SignUpSignIn.xml](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/blob/master/SocialAndLocalAccounts/SignUpOrSignin.xml)）會指定 Azure AD B2C 將執行的使用者旅程圖。 尋找 [信賴](../articles/active-directory-b2c/relyingparty.md)憑證者內的 **>referenceid** 元素。 更新  **ReferenceId** ，使其符合您在其中新增身分識別提供者的使用者旅程圖識別碼。 

在下列範例中，在 `CustomSignUpOrSignIn` 使用者旅程圖中， **ReferenceId** 設定為 `CustomSignUpOrSignIn` ：

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="CustomSignUpSignIn" />
  ...
</RelyingParty>
```

### <a name="upload-the-custom-policy"></a>上傳自訂原則

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 在入口網站工具列中選取 **目錄 + 訂用帳戶** 圖示，然後選取包含 Azure AD B2C 租用戶的目錄。
1. 在 Azure 入口網站中，搜尋並選取 [Azure AD B2C]。
1. 在 [原則] 之下，選取 [Identity Experience Framework]。
1. 選取 [ **上傳自訂原則**]，然後依下列順序上傳您變更的兩個原則檔案：擴充原則（例如 `TrustFrameworkExtensions.xml` ），然後是信賴憑證者原則（例如） `SignUpSignIn.xml` 。



