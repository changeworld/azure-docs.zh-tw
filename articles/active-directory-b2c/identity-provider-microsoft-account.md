---
title: 使用 Microsoft 帳戶設置註冊和登錄
titleSuffix: Azure AD B2C
description: 使用 Azure 活動目錄 B2C 在應用程式中使用 Microsoft 帳戶向客戶提供註冊和登錄。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 25784eb161a860398b0741d1d20375cabd1c4eca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188013"
---
# <a name="set-up-sign-up-and-sign-in-with-a-microsoft-account-using-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 設定註冊，並以 Microsoft 帳戶登入

## <a name="create-a-microsoft-account-application"></a>建立 Microsoft 帳戶應用程式

要在 Azure 活動目錄 B2C（Azure AD B2C）中使用 Microsoft 帳戶作為[標識提供程式](openid-connect.md)，需要在 Azure AD 租戶中創建應用程式。 此 Azure AD 租用戶與您的 Azure AD B2C 租用戶不同。 如果您還沒有 Microsoft 帳戶，則可以在 獲取[https://www.live.com/](https://www.live.com/)一個帳戶。

1. 登錄到 Azure[門戶](https://portal.azure.com)。
1. 通過在頂部功能表中選擇**目錄 + 訂閱**篩選器並選擇包含 Azure AD 租戶的目錄，請確保使用的目錄包含 Azure AD 租戶。
1. 選擇 Azure 入口網站左上角的 [所有服務]****，然後搜尋並選取 [應用程式註冊]****。
1. 選取 [新增註冊]****。
1. 輸入應用程式**的名稱**。 例如 *MSAapp1*。
1. 在 **"支援帳戶類型**"下，選擇**任何組織目錄中的帳戶和個人 Microsoft 帳戶（例如 Skype、Xbox、Outlook.com）。** 此選項針對最廣泛的 Microsoft 標識集。

   有關不同帳戶類型選擇的詳細資訊，請參閱[快速入門：使用 Microsoft 標識平臺註冊應用程式](../active-directory/develop/quickstart-register-app.md)。
1. 在**重定向 URI（可選）** 下，選擇 **"Web"** 並在文字方塊中輸入`https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`。 替換為`your-tenant-name`Azure AD B2C 租戶名稱。
1. 選擇 **"註冊"**
1. 記錄應用程式概述頁上顯示**的應用程式（用戶端）ID。** 在下一節中配置標識提供程式時，需要這樣做。
1. 選擇**證書&機密**
1. 按一下 **"新用戶端"機密**
1. 輸入機密**的描述**，例如*應用程式密碼 1，* 然後按一下"**添加**"。
1. 記錄 **"值"** 列中顯示的應用程式密碼。 在下一節中配置標識提供程式時，需要這樣做。

## <a name="configure-a-microsoft-account-as-an-identity-provider"></a>將 Microsoft 帳戶設為識別提供者

1. 以 Azure AD B2C 租戶的全域管理員身份登錄到[Azure 門戶](https://portal.azure.com/)。
1. 選取頂端功能表中的 [目錄 + 訂用帳戶]**** 篩選，然後選擇包含您租用戶的目錄，以確定您使用的是包含 Azure AD B2C 租用戶的目錄。
1. 選擇 Azure 入口網站左上角的 [所有服務]****，搜尋並選取 [Azure AD B2C]****。
1. 選擇**標識提供程式**，然後選擇**微軟帳戶**。
1. 輸入**名稱**。 例如 *，MSA*。
1. 對於**用戶端 ID**，輸入前面創建的 Azure AD 應用程式的應用程式（用戶端）ID。
1. 對於**用戶端機密**，輸入您記錄的客戶金鑰。
1. 選取 [儲存]****。
