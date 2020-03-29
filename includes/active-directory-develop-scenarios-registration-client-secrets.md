---
title: 包含檔案
description: 包括用於機密用戶端方案登錄頁的檔（守護進程、Web 應用、Web API）
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
ms.date: 04/18/2018
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: a5d34ac7eea50b67bd679d8cb8ddecf7ca277abd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76773368"
---
## <a name="register-secrets-or-certificates"></a>註冊機密或證書

對於任何機密的用戶端應用程式，您需要註冊機密或證書。 您可以通過[Azure 門戶](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview)中的互動式體驗或使用命令列工具（如 PowerShell）來註冊應用程式機密。

### <a name="register-client-secrets-by-using-the-application-registration-portal"></a>使用應用程式註冊門戶註冊用戶端機密

用戶端憑據的管理發生在應用程式的**證書&機密**頁上：

![證書&機密頁](../articles/active-directory/develop/media/quickstart-update-azure-ad-app-preview/credentials-certificates-secrets.png)

- 應用程式金鑰（也稱為用戶端金鑰）由 Azure AD 在註冊機密用戶端應用程式期間生成。 當您選擇 **"新用戶端"時**，將發生此生成。 此時，您必須將機密字串複製到剪貼簿以在應用中使用，然後才能選擇 **"保存**"。 此字串將不再顯示。
- 在應用程式註冊期間，您可以使用 **"上傳證書"** 按鈕上載證書。 Azure AD 僅支援直接在應用程式上註冊的證書，並且不遵循憑證連結。

有關詳細資訊，請參閱[快速入門：配置用戶端應用程式以訪問 Web API |向應用程式添加憑據](../articles/active-directory/develop/quickstart-configure-app-access-web-apis.md#add-credentials-to-your-web-application)。



### <a name="register-client-secrets-by-using-powershell"></a>使用 PowerShell 註冊用戶端機密

或者，您可以使用命令列工具向 Azure AD 註冊應用程式。 [活動目錄-dotnetcore-守護進程-v2](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2)示例演示如何向 Azure AD 應用程式註冊應用程式金鑰或證書：

- 有關如何註冊應用程式金鑰的詳細資訊，請參閱[應用程式創建腳本/配置.ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts/Configure.ps1#L190)。
- 有關如何向應用程式註冊證書的詳細資訊，請參閱[使用 Cert/配置.ps1 的應用程式創建腳本](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts-withCert/Configure.ps1#L162-L178)。
