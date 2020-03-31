---
title: 使用 Google 帳戶設置註冊和登錄
titleSuffix: Azure AD B2C
description: 使用 Azure Active Directory B2C，讓具有 Google 帳戶的客戶得以註冊和登入您的應用程式。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 48955caddb64069f897078f5e47066d9f11d119b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188135"
---
# <a name="set-up-sign-up-and-sign-in-with-a-google-account-using-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 設定註冊，並以 Google 帳戶登入

## <a name="create-a-google-application"></a>建立 Google 應用程式

要在 Azure 活動目錄 B2C（Azure AD B2C） 中使用 Google 帳戶作為[標識提供程式](authorization-code-flow.md)，需要在 Google 開發人員主控台中創建應用程式。 如果您還沒有 Google 帳戶，您可以在 註冊[https://accounts.google.com/SignUp](https://accounts.google.com/SignUp)。

1. 以您的 Google 帳戶認證登入 [Google 開發人員主控台](https://console.developers.google.com/)。
1. 在頁面的左上角，選擇專案清單，然後選擇 **"新專案**"。
1. 輸入**專案名稱**，選擇 **"創建**"。
1. 通過在螢幕左上角選擇專案下拉清單，按名稱選擇專案，然後選擇 **"打開**"，確保使用新專案。
1. 選擇左側功能表中的 **"OAuth 同意"螢幕**，選擇 **"外部**"，然後選擇"**創建**"。
輸入應用程式**的名稱**。 在 **"授權域**"部分輸入*b2clogin.com，* 然後選擇 **"保存**"。
1. 在左側功能表選取 [認證]****，然後選取 [建立認證]**** > [Oauth 用戶端識別碼]****。
1. 在 [應用程式類型]**** 下方，選取 [Web 應用程式]****。
1. 輸入應用程式的**名稱**，在 [授權 JavaScript 來源]**** 中輸入 `https://your-tenant-name.b2clogin.com`，接著在 [授權重新導向 URI]**** 中輸入 `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`。 以您的租用戶名稱取代 `your-tenant-name`。 即使租用戶在 Azure AD B2C 中是使用大寫字母來定義的，您還是需要在輸入租用戶名稱時，全部使用小寫字母。
1. 按一下 **[建立]**。
1. 複製**用戶端 ID**和**用戶端機密**的值。 您必須使用這兩個值，將 Google 設為租用戶中的身分識別提供者。 **用戶端金鑰**是重要的安全憑據。

## <a name="configure-a-google-account-as-an-identity-provider"></a>將 Google 帳戶設為識別提供者

1. 以 Azure AD B2C 租戶的全域管理員身份登錄到[Azure 門戶](https://portal.azure.com/)。
1. 選取頂端功能表中的 [目錄 + 訂用帳戶]**** 篩選，然後選擇包含您租用戶的目錄，以確定您使用的是包含 Azure AD B2C 租用戶的目錄。
1. 選擇 Azure 入口網站左上角的 [所有服務]****，搜尋並選取 [Azure AD B2C]****。
1. 選擇**身份供應商**，然後選擇**谷歌**。
1. 輸入**名稱**。 例如，*谷歌*。
1. 對於**用戶端 ID，** 輸入您之前創建的 Google 應用程式的用戶端 ID。
1. 對於**客戶機密**，輸入您記錄的客戶金鑰。
1. 選取 [儲存]****。
