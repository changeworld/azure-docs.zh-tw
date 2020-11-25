---
title: 包含檔案
description: '包含機密用戶端案例登陸頁面的檔案 (daemon、web 應用程式、web API) '
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/14/2020
ms.author: jmprieur
ms.openlocfilehash: 42102f38959911388cefcc141d949e59f24a2c31
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "95995996"
---
## <a name="add-a-client-secret-or-certificate"></a>新增用戶端密碼或憑證

如同任何機密用戶端應用程式，您需要新增秘密或 *憑證* 來作為該應用程式的認證，讓它本身可以驗證，而不需要使用者互動。

您可以使用 [Azure 入口網站](#add-client-credentials-by-using-the-azure-portal) 或使用命令列工具（例如 [PowerShell](#add-client-credentials-by-using-powershell)）將認證新增至用戶端應用程式的註冊。

### <a name="add-client-credentials-by-using-the-azure-portal"></a>使用 Azure 入口網站新增用戶端認證

若要將認證新增至您的機密用戶端應用程式的應用程式註冊，請遵循 [快速入門：使用 Microsoft 身分識別平臺註冊應用程式](../articles/active-directory/develop/quickstart-register-app.md) ，以瞭解您想要新增的認證類型：

* [新增用戶端密碼](../articles/active-directory/develop/quickstart-register-app.md#add-a-client-secret)
* [新增憑證](../articles/active-directory/develop/quickstart-register-app.md#add-a-certificate)

### <a name="add-client-credentials-by-using-powershell"></a>使用 PowerShell 新增用戶端認證

或者，您可以在使用 PowerShell 向 Microsoft 身分識別平臺註冊應用程式時，新增認證。

GitHub 上的 [active directory-dotnetcore-daemon-v2](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) 程式碼範例會顯示如何在註冊應用程式時新增應用程式密碼或憑證：

- 如需有關如何使用 PowerShell 新增 **用戶端密碼** 的詳細資訊，請參閱 [AppCreationScripts/Configure.ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts/Configure.ps1#L190)。
- 如需如何使用 PowerShell 新增 **憑證** 的詳細資訊，請參閱 [AppCreationScripts-withCert/Configure.ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts-withCert/Configure.ps1#L162-L178)。
