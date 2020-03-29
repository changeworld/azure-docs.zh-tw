---
title: 使用 Twitter 帳戶設置註冊和登錄
titleSuffix: Azure AD B2C
description: 使用 Azure Active Directory B2C，讓具有 Twitter 帳戶的客戶得以註冊和登入您的應用程式。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 62a283efb93987d3c4a6564c9b25d2031c269559
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80051456"
---
# <a name="set-up-sign-up-and-sign-in-with-a-twitter-account-using-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 設定註冊，並以 Twitter 帳戶登入

## <a name="create-an-application"></a>建立應用程式

若要使用 Twitter 作為 Azure AD B2C 中的識別提供者，您需要建立 Twitter 應用程式。 如果您還沒有 Twitter 帳戶，您可以在 註冊[https://twitter.com/signup](https://twitter.com/signup)。

1. 使用您的 Twitter 帳戶認證登入 [Twitter 開發人員](https://developer.twitter.com/en/apps) \(英文\) 網站。
1. 選擇 **"創建應用**"。
1. 輸入**應用程式名稱**和**應用程式說明**。
1. 在 [Website URL]**** \(網站 URL\) 中，輸入 `https://your-tenant.b2clogin.com`。 以您的租用戶名稱取代 `your-tenant`。 例如： `https://contosob2c.b2clogin.com` 。
1. 在 [回呼 URL]**** 中輸入 `https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/your-user-flow-Id/oauth1/authresp`。 將 `your-tenant` 取代為您的租用戶名稱，並將 `your-user-flow-Id` 取代為您的使用者流程識別碼。 例如： `b2c_1A_signup_signin_twitter` 。 輸入租戶名稱和使用者流 ID 時，需要使用所有小寫字母，即使這些小寫字母在 Azure AD B2C 中使用大寫字母定義也是如此。
1. 在頁面底部，閱讀並接受條款，然後選取 [Create]**** \(建立\)。
1. 在 [App details]**** \(應用程式詳細資料\) 頁面上，選取 [Edit] \(編輯\) > [Edit details] \(編輯詳細資料\)****、核取 [Enable Sign in with Twitter]**** \(使用 Twitter 啟用登入\) 的方塊，然後選取 [Save]**** \(儲存\)。
1. 選取 [Keys and tokens]**** \(金鑰和權杖\)，並記錄**取用者 API 金鑰**和**取用者 API 祕密金鑰**的值，以供稍後使用。

## <a name="configure-twitter-as-an-identity-provider-in-your-tenant"></a>在租用戶中將 Twitter 設為識別提供者

1. 以 Azure AD B2C 租戶的全域管理員身份登錄到[Azure 門戶](https://portal.azure.com/)。
1. 選取頂端功能表中的 [目錄 + 訂用帳戶]**** 篩選，然後選擇包含您租用戶的目錄，以確定您使用的是包含 Azure AD B2C 租用戶的目錄。
1. 選擇 Azure 入口網站左上角的 [所有服務]****，搜尋並選取 [Azure AD B2C]****。
1. 選擇**身份提供者**，然後選擇**推特**。
1. 輸入**名稱**。 例如，*推特*。
1. 對於**用戶端 ID，** 輸入您之前創建的 Twitter 應用程式的消費者 API 金鑰。
1. 對於**用戶端金鑰**，輸入您錄製的消費者 API 金鑰。
1. 選取 [儲存]****。
