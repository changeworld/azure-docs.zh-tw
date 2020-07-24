---
title: 包含檔案
description: 包含機密用戶端案例登陸頁面（daemon、web 應用程式、Web API）的檔案
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/15/2020
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: a3acdbb93dd20f0b89e4f99d64f5f7a30ce40623
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87102520"
---
## <a name="register-secrets-or-certificates"></a>註冊秘密或憑證

對於任何機密用戶端應用程式，您都必須註冊秘密或憑證。 您可以透過[Azure 入口網站](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview)中的互動式體驗，或使用命令列工具（例如 PowerShell）來註冊您的應用程式秘密。

### <a name="register-client-secrets-by-using-the-application-registration-portal"></a>使用應用程式註冊入口網站註冊用戶端秘密

用戶端認證的管理會在應用程式的 [**憑證 & 秘密**] 頁面上進行：

![[憑證 & 秘密] 頁面](../articles/active-directory/develop/media/quickstart-update-azure-ad-app-preview/credentials-certificates-secrets.png)

- 若要建立*用戶端密碼*，請在 Azure 入口網站的應用程式註冊中選取 [**新增用戶端密碼**]。 當您建立用戶端密碼時，_必須_先記錄密碼的字串，然後才能離開 [**憑證 & 秘密**] 窗格。 秘密的字串一律不會再次顯示。
- 在應用程式註冊期間，您可以使用 [**上傳憑證**] 按鈕來上傳憑證。 Azure AD 僅支援在應用程式上直接註冊，而不會遵循憑證鏈的憑證。

如需詳細資訊，請參閱[快速入門：設定用戶端應用程式以存取 Web api |將認證新增至您的應用程式](../articles/active-directory/develop/quickstart-configure-app-access-web-apis.md#add-credentials-to-your-web-application)。

### <a name="register-client-secrets-by-using-powershell"></a>使用 PowerShell 註冊用戶端秘密

或者，您也可以使用命令列工具，向 Azure AD 註冊應用程式。 [Active directory-dotnetcore-daemon-v2](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2)範例示範如何使用 Azure AD 應用程式來註冊應用程式密碼或憑證：

- 如需如何註冊應用程式密碼的詳細資訊，請參閱[AppCreationScripts/Configure.ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts/Configure.ps1#L190)。
- 如需如何向應用程式註冊憑證的詳細資訊，請參閱[AppCreationScripts-withCert/Configure.ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts-withCert/Configure.ps1#L162-L178)。
