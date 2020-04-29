---
title: 設定註冊，並以 Twitter 帳戶登入
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80051456"
---
# <a name="set-up-sign-up-and-sign-in-with-a-twitter-account-using-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 設定註冊，並以 Twitter 帳戶登入

## <a name="create-an-application"></a>建立應用程式

若要使用 Twitter 作為 Azure AD B2C 中的識別提供者，您需要建立 Twitter 應用程式。 如果您還沒有 Twitter 帳戶，可以在[https://twitter.com/signup](https://twitter.com/signup)註冊。

1. 使用您的 Twitter 帳戶認證登入 [Twitter 開發人員](https://developer.twitter.com/en/apps) \(英文\) 網站。
1. 選取 [**建立應用程式**]。
1. 輸入**應用程式名稱**和**應用程式說明**。
1. 在 [Website URL]**** \(網站 URL\) 中，輸入 `https://your-tenant.b2clogin.com`。 以您的租用戶名稱取代 `your-tenant`。 例如： `https://contosob2c.b2clogin.com` 。
1. 在 [回呼 URL]**** 中輸入 `https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/your-user-flow-Id/oauth1/authresp`。 將 `your-tenant` 取代為您的租用戶名稱，並將 `your-user-flow-Id` 取代為您的使用者流程識別碼。 例如： `b2c_1A_signup_signin_twitter` 。 輸入您的租使用者名稱和使用者流程識別碼時，您必須全部使用小寫字母，即使在 Azure AD B2C 中使用大寫字母來定義也一樣。
1. 在頁面底部，閱讀並接受條款，然後選取 [Create]**** \(建立\)。
1. 在 [App details]**** \(應用程式詳細資料\) 頁面上，選取 [Edit] \(編輯\) > [Edit details] \(編輯詳細資料\)****、核取 [Enable Sign in with Twitter]**** \(使用 Twitter 啟用登入\) 的方塊，然後選取 [Save]**** \(儲存\)。
1. 選取 [Keys and tokens]**** \(金鑰和權杖\)，並記錄**取用者 API 金鑰**和**取用者 API 祕密金鑰**的值，以供稍後使用。

## <a name="configure-twitter-as-an-identity-provider-in-your-tenant"></a>在租用戶中將 Twitter 設為識別提供者

1. 以 Azure AD B2C 租使用者的全域管理員身分登入[Azure 入口網站](https://portal.azure.com/)。
1. 選取頂端功能表中的 [目錄 + 訂用帳戶]  篩選，然後選擇包含您租用戶的目錄，以確定您使用的是包含 Azure AD B2C 租用戶的目錄。
1. 選擇 Azure 入口網站左上角的 [所有服務]****，搜尋並選取 [Azure AD B2C]****。
1. 選取 [**識別提供者**]，然後選取 [ **Twitter**]。
1. 輸入 [**名稱**]。 例如， *Twitter*。
1. 針對 [**用戶端識別碼**]，輸入您稍早建立之 Twitter 應用程式的取用者 API 金鑰。
1. 針對 [**用戶端密碼**]，輸入您所記錄的取用者 API 秘密金鑰。
1. 選取 [儲存]  。
