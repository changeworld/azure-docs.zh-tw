---
title: 新版 Azure 入口網站應用程式註冊體驗
titleSuffix: Microsoft identity platform
description: Azure 門戶中新的應用註冊體驗簡介
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 11/8/2019
ms.author: marsma
ms.reviewer: lenalepa, alamaral
ms.custom: aaddev
ms.openlocfilehash: 50c88dd1785bd9177219054fed3800ca725a5274
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154588"
---
# <a name="the-new-azure-portal-app-registration-experience"></a>新的 Azure 門戶應用註冊體驗

Azure 門戶中的新[應用註冊](https://go.microsoft.com/fwlink/?linkid=2083908)體驗有許多改進。 如果您更熟悉註冊或管理融合應用程式（稱為舊體驗）的應用程式註冊門戶 （apps.dev.microsoft.com） 體驗，本培訓指南將讓您開始使用新體驗。

## <a name="whats-not-changing"></a>什麼沒有改變？

- 您的應用程式和相關配置可以在新體驗中找到。 您無需再次註冊應用程式，應用程式的使用者也不需要再次登錄。

    > [!NOTE]
    > 您必須使用用於註冊應用程式以在 Azure 門戶中查找應用程式的帳戶登錄。 我們建議您通過比較設定檔中的電子郵件地址，在 Azure 門戶中檢查登錄使用者與登錄到應用程式註冊門戶的使用者匹配。
    >
    > 在某些情況下，特別是當您使用帶有 Azure AD 電子郵件地址的個人 Microsoft 帳戶（例如 Outlook、Live、Xbox 等）登錄時，我們發現，當您從舊體驗轉到 Azure 門戶時，它會用相同的標記您進入其他帳戶電子郵件在 Azure AD 租戶中。 如果您仍然認為您的應用程式丟失，請登出並使用正確的帳戶登錄。

- Azure 門戶中尚不支援使用個人 Microsoft 帳戶創建的即時 SDK 應用，在不久的將來將繼續保留舊體驗。

## <a name="key-changes"></a>主要更改

-   在舊體驗中，應用預設註冊為支援所有組織帳戶（多租戶）和個人 Microsoft 帳戶的聚合應用。 無法通過舊體驗修改此功能，因此很難創建僅支援組織帳戶的應用（多租戶或單個租戶）。
    新的體驗允許您註冊支援所有這些選項的應用。 [瞭解有關應用類型 的更多](active-directory-v2-registration-portal.md)資訊。

-   在新的體驗中，如果個人 Microsoft 帳戶也位於 Azure AD 租戶中，您將看到三個選項卡- 租戶中的所有應用程式、租戶中擁有的應用程式以及個人帳戶中的應用程式。 因此，如果您認為在個人 Microsoft 帳戶註冊的應用丟失，請查看**來自個人帳戶的應用程式**選項卡。

-   在新的體驗中，您可以通過導航到設定檔並選擇交換器目錄，輕鬆地在租戶之間切換。

## <a name="list-of-applications"></a>應用程式清單

-   新應用清單顯示通過 Azure 門戶中的舊應用註冊體驗（僅登錄 Azure AD 帳戶的應用）以及通過[應用程式註冊門戶](https://apps.dev.microsoft.com/)（同時登錄 Azure AD 和個人 Microsoft 帳戶的應用）註冊的應用程式。

-   新應用清單有兩個附加列：**在列上創建**，**證書&機密**列，顯示已在應用上註冊的憑據的狀態（當前、即將過期或過期）。

## <a name="new-app-registration"></a>新增應用程式註冊

在舊體驗中，要註冊融合應用，您只需要提供名稱。 創建的應用註冊為支援所有組織目錄（多租戶）以及個人 Microsoft 帳戶的聚合應用。  無法通過舊體驗修改此功能，因此很難創建僅支援組織帳戶的應用（多租戶或單個租戶）。 [瞭解有關受支援帳戶類型詳細資訊](v2-supported-account-types.md)

在新體驗中，必須為應用提供名稱並選擇"支援帳戶類型"。 您可以選擇提供重定向 URI。
如果提供重定向 URI，則需要指定它是 Web/公共的（本機/移動和桌面）。 有關如何使用新應用註冊體驗註冊應用的詳細資訊，請參閱[此快速入門](quickstart-register-app.md)。

## <a name="app-management-page"></a>應用管理頁面

舊體驗具有用於融合應用的單個應用管理頁，該頁面包含以下部分：屬性、應用程式機密、平臺、擁有者、Microsoft 圖形許可權、設定檔和高級選項。

Azure 門戶中的新體驗將這些功能表示為單獨的頁面。 您可以在其中找到等效功能：

-   屬性 - 名稱和應用程式 ID 位於"概述"頁上。

-   應用程式機密位於證書&機密頁上

-   平台佈建位於身份驗證頁上

-   Microsoft 圖形許可權與其他許可權一起位於 API 許可權頁上

-   個人資料位於品牌頁面上

-   高級選項 - 即時 SDK 支援位於身份驗證頁面上。

## <a name="application-secretscertificates--secrets"></a>應用程式機密/證書&機密

在新的體驗中，**應用程式機密**已重命名為**證書&機密**。 此外，**公開金鑰**稱為**證書**，**密碼**稱為**用戶端機密**。 出於安全原因，我們選擇不將此功能引入新體驗中，因此，您無法再生成新的金鑰組。

## <a name="platformsauthentication-reply-urlsredirect-uris"></a>平臺/身份驗證：回復 URL/重定向 URI
在舊體驗中，應用具有 Web、本機和 Web API 的平臺部分，用於配置重定向 URL、登出 URL 和隱式流。

在新體驗中，可以在應用\'的身份驗證部分找到回復 URL。 此外，它們稱為重定向 URI，重定向 URI 的格式已更改。 它們需要與應用類型（Web 或公共用戶端 - 移動用戶端和桌面）相關聯。 [深入了解](quickstart-configure-app-access-web-apis.md#add-redirect-uris-to-your-application)

Web API 在"公開 API"頁中配置。

> [!NOTE]
> 試用新的驗證設定體驗，您可以在其中根據您要設為目標的平台或裝置來設定應用程式的設定。 [深入了解](quickstart-configure-app-access-web-apis.md#configure-platform-settings-for-your-application)

## <a name="microsoft-graph-permissionsapi-permissions"></a>微軟圖形許可權/API 許可權

-   在舊體驗中選擇 API 時，只能從 Microsoft 圖形 API 中進行選擇。 在新的體驗中，您可以從許多 Microsoft API 中進行選擇，包括 Microsoft 圖形、組織的 API 和 API，這三個選項卡顯示：Microsoft API、我的組織使用的 API 或我的 API。 組織使用選項卡搜索租戶中的服務主體的 API 上的搜索欄。

    > [!NOTE]
    > 如果應用程式未與租戶關聯，則看不到此選項卡。 有關如何使用新體驗請求許可權的詳細資訊，請參閱[此快速入門](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory/develop/quickstart-configure-app-access-web-apis.md)。

-   舊體驗沒有 **"授予許可權**"按鈕。 在新體驗中，應用的 API 許可權部分上有一個"授予同意"部分，該節帶有 **"授予管理員同意"** 按鈕。 只有管理員可以授予同意，並且此按鈕僅為管理員啟用。 當管理員選擇 **"授予管理員同意"** 按鈕時，將授予所有請求的版權管理員同意。

## <a name="profile"></a>設定檔
在舊體驗中，設定檔具有徽標、主頁 URL、服務條款 URL 和隱私權聲明 URL 配置。 在新的體驗中，可以在"品牌"頁面找到這些。

## <a name="application-manifest"></a>應用程式資訊清單
在新體驗中，清單頁面允許您編輯和更新應用的屬性。 如需詳細資訊，請參閱[應用程式資訊清單](reference-app-manifest.md)。

## <a name="new-ui"></a>新 UI
對於以前只能使用清單編輯器或 API 設置或不存在的屬性，有新的 UI。

-   隱式授予流（身份驗證2Allow隱式流）可以在身份驗證頁面上找到。 與舊體驗不同，您可以啟用訪問權杖或 ID 權杖，或同時啟用這兩種權杖。

-   可通過"公開 API"頁配置由此 API 定義的作用域（auth2 許可權）和授權用戶端應用程式（預授權應用程式）。 有關如何將應用配置為 Web API 並公開許可權/作用域的詳細資訊，請參閱[此快速入門](quickstart-configure-app-expose-web-apis.md)。

-   發佈商域（在[\'應用程式同意提示](application-consent-experience.md)符上向使用者顯示）可以在"品牌"邊欄選項卡頁面上找到。 有關如何配置發行者域的詳細資訊，請參閱[此操作操作](howto-configure-publisher-domain.md)。

## <a name="limitations"></a>限制

新體驗具有以下限制：

-   新體驗尚不支援 Azure AD B2C 租戶的應用註冊。

-   新體驗尚不支援使用個人 Microsoft 帳戶創建的即時 SDK 應用。

-   UI 不支援更改受支援帳戶的值。 您需要使用應用清單，除非您\'在 Azure AD 單租戶和多租戶之間切換。

   > [!NOTE]
   > 如果您是 Azure AD 租戶中的個人 Microsoft 帳戶使用者，並且租戶管理員對 Azure 門戶的訪問受到限制，則可能會拒絕訪問。 但是，如果您在搜索欄中鍵入應用註冊或固定該快捷方式來訪問新體驗。
