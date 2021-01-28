---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 01/27/2021
ms.author: mimart
ms.openlocfilehash: 4ca47bf5b99588fbe4efd91a9211ee0309c1892c
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98951513"
---
## <a name="test-your-custom-policy"></a>測試您的自訂原則

1. 例如，請選取您的信賴憑證者原則 `B2C_1A_signup_signin` 。
1. 針對 [ **應用程式**]，選取您 [先前註冊](../articles/active-directory-b2c/troubleshoot-custom-policies.md#troubleshoot-the-runtime)的 web 應用程式。 **Reply URL** 應顯示 `https://jwt.ms`。
1. 選取 [ **立即執行** ] 按鈕。

如果登入程式成功，則會將瀏覽器重新導向至 `https://jwt.ms` ，以顯示 Azure AD B2C 所傳回的權杖內容。

