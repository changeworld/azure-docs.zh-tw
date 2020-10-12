---
title: 公用和機密用戶端應用程式 (MSAL) |蔚藍
titleSuffix: Microsoft identity platform
description: " (MSAL) 瞭解 Microsoft 驗證程式庫中的公用用戶端和機密用戶端應用程式。"
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/25/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 9c3292a31e5f750c16933acf94509e0ad226080a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "81534307"
---
# <a name="public-client-and-confidential-client-applications"></a>公用用戶端和機密用戶端應用程式
Microsoft 驗證程式庫 (MSAL) 會定義兩種類型的用戶端：公用用戶端和機密用戶端。 這兩種用戶端類型的辨識方式是能夠安全地向授權伺服器進行驗證，並維護其用戶端認證的機密性。 相反地，Azure AD 驗證程式庫 (ADAL) 會使用所謂的 *驗證* 內容 (這是 Azure AD) 的連接。

- **機密用戶端應用程式** 是在伺服器上執行的應用程式 (web 應用程式、web API 應用程式，或甚至是服務/守護程式應用程式) 。 他們被視為很難存取，而且基於這個原因，能夠保留應用程式秘密。 機密用戶端可以保留設定時間秘密。 用戶端的每個實例都有不同的設定 (包括用戶端識別碼和用戶端密碼) 。 這些值對終端使用者而言很難解壓縮。 Web 應用程式是最常見的機密用戶端。 用戶端識別碼是透過網頁瀏覽器公開，但秘密只會在後端通道中傳遞，而不會直接公開。

    機密用戶端應用程式： <BR>
    ![Web 應用程式 ](media/msal-client-applications/web-app.png) ![ web API ](media/msal-client-applications/web-api.png) ![ Daemon/服務](media/msal-client-applications/daemon-service.png)

- **公用用戶端應用程式** 是在裝置或桌上型電腦或網頁瀏覽器中執行的應用程式。 它們不受信任以安全地保存應用程式秘密，因此只會代表使用者存取 web Api。  (僅支援公用用戶端流程。 ) 公用用戶端無法保存設定時間秘密，因此沒有用戶端密碼。

    公用用戶端應用程式： <BR>
    ![傳統型應用程式 ](media/msal-client-applications/desktop-app.png) ![ 無 API 行動 ](media/msal-client-applications/browserless-app.png) ![ 應用程式](media/msal-client-applications/mobile-app.png)

> [!NOTE]
> 在 MSAL.js 中，不會區分公用和機密用戶端應用程式。  MSAL.js 將用戶端應用程式表示為以使用者代理程式為基礎的應用程式、在使用者代理程式（例如網頁瀏覽器）中執行用戶端程式代碼的公用用戶端。 這些用戶端不會儲存秘密，因為瀏覽器內容可供公開存取。

## <a name="comparing-the-client-types"></a>比較用戶端類型
以下是公用用戶端和機密用戶端應用程式之間的一些相似之處和差異：

- 這兩種類型的應用程式都會維護使用者權杖快取，而且可以在權杖已在權杖快取) 中時，以無訊息方式取得權杖 (。 機密用戶端應用程式也有應用程式權杖的應用程式權杖快取，適用于應用程式本身的權杖。
- 這兩種類型的應用程式都會管理使用者帳戶，而且可以從使用者權杖快取取得帳戶、從其識別碼取得帳戶，或移除帳戶。
- 公用用戶端應用程式有四種方式可取得權杖 (四個驗證流程) 。 機密用戶端應用程式有三種方式可取得權杖 (以及用來計算身分識別提供者授權端點) 之 URL 的一種方式。 如需詳細資訊，請參閱取得 [權杖](msal-acquire-cache-tokens.md)。

如果您已經使用 ADAL，您可能會注意到，與 ADAL 的驗證內容不同的是，MSAL 用戶端識別碼 (也稱為 *應用* 程式識別碼或 *應用程式識別碼*) 會在應用程式的結構中傳遞一次。 當應用程式取得權杖時，不需要再次傳遞。 這對公用和機密用戶端應用程式都是如此。 機密用戶端應用程式的函式也會傳遞用戶端認證：它們與身分識別提供者共用的密碼。

## <a name="next-steps"></a>接下來的步驟
了解：
- [用戶端應用程式設定選項](msal-client-application-configuration.md)
- [使用 MSAL.NET 來具現化用戶端應用程式](msal-net-initializing-client-applications.md)
- [使用 MSAL.js來具現化用戶端應用程式 ](msal-js-initializing-client-applications.md)
