---
title: 使用 Amazon 帳戶設定註冊和登入
titleSuffix: Azure AD B2C
description: 使用 Azure Active Directory B2C，讓具有 Amazon 帳戶的客戶得以註冊和登入您的應用程式。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/05/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d1a771cb13fcfa76449500ad71c67dcf7c446fa4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "85388437"
---
# <a name="set-up-sign-up-and-sign-in-with-an-amazon-account-using-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 設定註冊，並以 Amazon 帳戶登入

## <a name="create-an-app-in-the-amazon-developer-console"></a>在 Amazon 開發人員主控台中建立應用程式

若要在 Azure Active Directory B2C (Azure AD B2C) 中使用 Amazon 帳戶作為同盟身分識別提供者，您需要在 [Amazon 開發人員服務和技術](https://developer.amazon.com)中建立應用程式。 如果您還沒有 Amazon 帳戶，您可以在中註冊 [https://www.amazon.com/](https://www.amazon.com/) 。

> [!NOTE]  
> 在以下 **步驟 8** 中使用下列 url， `your-tenant-name` 並以您的租使用者名稱取代。 輸入您的租使用者名稱時，即使租使用者是在 Azure AD B2C 中以大寫字母定義，也請使用所有小寫字母。
> - 針對 [ **允許的來源**]，輸入 `https://your-tenant-name.b2clogin.com` 
> - 針對 **允許**的傳回 url，輸入 `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`

[!INCLUDE [identity-provider-amazon-idp-register.md](../../includes/identity-provider-amazon-idp-register.md)]

## <a name="configure-an-amazon-account-as-an-identity-provider"></a>將 Amazon 帳戶設為識別提供者

1. 以 Azure AD B2C 租用戶的全域管理員身分登入 [Azure 入口網站](https://portal.azure.com/)。
1. 選取頂端功能表中的 [目錄 + 訂用帳戶] 篩選，然後選擇包含您租用戶的目錄，以確定您使用的是包含 Azure AD B2C 租用戶的目錄。
1. 選擇 Azure 入口網站左上角的 [所有服務]，搜尋並選取 [Azure AD B2C]。
1. 選取 [ **識別提供者**]，然後選取 [ **Amazon**]。
1. 輸入 [名稱]。 例如， *Amazon*。
1. 針對 [ **用戶端識別碼**]，輸入您稍早建立的 Amazon 應用程式的用戶端識別碼。
1. 針對 **用戶端密碼**，請輸入您所記錄的用戶端密碼。
1. 選取 [儲存]****。
