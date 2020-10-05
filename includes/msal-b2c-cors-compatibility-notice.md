---
author: mmacy
ms.service: active-directory
ms.subservice: develop
ms.topic: include
ms.date: 07/27/2020
ms.author: marsma
ms.openlocfilehash: 99ca8e45da27f5bce5258d55f46bcfa25b358239
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "87311531"
---
> [!IMPORTANT]
> MSAL.js 2.0 目前不支援 Azure AD B2C 與 PKCE 授權碼流程搭配使用。 目前，Azure AD B2C 建議使用以下文章中所述的隱含流程：[教學課程：註冊應用程式][implicit-flow]。 若要追蹤此問題的進度，請參閱 [MSAL.js Wiki][msal-wiki]。

[github-issue]: https://github.com/AzureAD/microsoft-authentication-library-for-js/issues/1795
[implicit-flow]: ../articles/active-directory-b2c/tutorial-register-applications.md
[msal-wiki]: https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/MSAL-browser-B2C-CORS-issue
