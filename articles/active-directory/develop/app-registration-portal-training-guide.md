---
title: 新版 Azure 入口網站應用程式註冊體驗
titleSuffix: Microsoft identity platform
description: Azure 入口網站中新的應用程式註冊體驗簡介
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 11/8/2019
ms.author: marsma
ms.reviewer: lenalepa, alamaral
ms.custom: aaddev
ms.openlocfilehash: 13242a41e1d10b0df031bf10fd646d9ec3cf47c3
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/03/2020
ms.locfileid: "89437763"
---
# <a name="the-new-azure-portal-app-registration-experience"></a>新的 Azure 入口網站應用程式註冊體驗

Azure 入口網站的新 [應用程式註冊](https://go.microsoft.com/fwlink/?linkid=2083908) 體驗有許多改進。 如果您更熟悉應用程式註冊入口網站 (apps.dev.microsoft.com) 來註冊或管理應用程式（在此稱為「舊體驗」），本指南將協助您開始使用新的體驗。

## <a name="whats-not-changing"></a>什麼不會變更？

- 在新的體驗中，您可以找到您的應用程式和相關設定。 您不需要再次註冊應用程式，應用程式的使用者就不需要再次登入。

    > [!NOTE]
    > 您必須使用您用來註冊應用程式的帳戶登入，才能在 Azure 入口網站中找到它們。 建議您透過比較設定檔中的電子郵件地址，在 Azure 入口網站中檢查登入的使用者是否符合已登入應用程式註冊入口網站的使用者。
    >
    > 在某些情況下，尤其是當您使用個人 Microsoft 帳戶登入時 (例如 Outlook、Live、Xbox 等 ) 使用 Azure AD 的電子郵件地址時，我們發現當您移至舊版體驗的 Azure 入口網站時，會將您登入到您的 Azure AD 租使用者中具有相同電子郵件的不同帳戶。 如果您仍然認為您的應用程式遺失，請登出並使用正確的帳戶登入。

- Azure 入口網站尚未支援使用個人 Microsoft 帳戶所建立的 Live SDK 應用程式，並將在不久的將來繼續維持舊的體驗。

## <a name="key-changes"></a>金鑰變更

-   在舊版體驗中，應用程式預設會註冊為 *融合* 式應用程式-支援所有組織帳戶的應用程式 (多租使用者) 和個人 Microsoft 帳戶。 這無法透過舊體驗進行修改，因此很難建立只支援組織帳戶 (多租使用者或單一租使用者) 的應用程式。
    新的體驗可讓您註冊支援所有這些選項的應用程式。 [深入瞭解應用程式類型](active-directory-v2-registration-portal.md)。

-   在新的體驗中，如果您的個人 Microsoft 帳戶也在 Azure AD 租使用者中，您將會看到三個索引標籤--租使用者中的所有應用程式、租使用者中擁有的應用程式，以及來自您個人帳戶的應用程式。 因此，如果您認為註冊個人 Microsoft 帳戶的應用程式遺失，請 **從 [個人帳戶** ] 索引標籤檢查應用程式。

-   在新的體驗中，您可以流覽至您的設定檔，然後選擇 [切換目錄]，輕鬆地在租使用者之間切換。

## <a name="list-of-applications"></a>應用程式清單

-   新的應用程式清單會顯示透過 Azure 入口網站 (應用程式中的繼承應用程式註冊體驗所註冊的應用程式，這些應用程式只會登入 Azure AD 帳戶) 以及透過 [應用程式註冊入口網站](https://apps.dev.microsoft.com/) 註冊的應用程式 (同時登入 Azure AD 和個人 Microsoft 帳戶) 的應用程式。

-   新的應用程式清單中有兩個額外的資料行：在 [資料行] 和 [**憑證] & [秘密**] 資料行**上**，顯示在應用程式上註冊的認證 (目前狀態、即將到期或過期) 。

## <a name="new-app-registration"></a>新增應用程式註冊

在舊體驗中，若要註冊應用程式，您只需要提供名稱。 所建立的應用程式已註冊為 *融合* 式應用程式-支援所有組織目錄的應用程式 (多租使用者) 和個人 Microsoft 帳戶。  這無法透過舊體驗進行修改，因此很難建立只支援組織帳戶 (單一或多租使用者) 的應用程式。 [深入瞭解支援的帳戶類型](v2-supported-account-types.md)

在新的體驗中，您必須提供應用程式的名稱，並選擇支援的帳戶類型。 您可以選擇性地提供重新導向 URI。
如果您提供重新導向 URI，您必須指定它是 web/public (原生/行動裝置和桌面) 。 如需如何使用新的應用程式註冊體驗來註冊應用程式的詳細資訊，請參閱 [本快速入門](quickstart-register-app.md)。

## <a name="app-management-page"></a>應用程式管理頁面

舊版體驗具有應用程式的單一應用程式管理頁面，其中包含下列各節：內容、應用程式秘密、平臺、擁有者、Microsoft Graph 許可權、設定檔和 Advanced 選項。

Azure 入口網站的新體驗將這些功能顯示在不同的頁面中。 您可以在這裡找到對等的功能：

- 屬性-名稱和應用程式識別碼位於 [總覽] 頁面上。
- 應用程式秘密位於憑證 & 秘密頁面
- 平臺設定位於 [驗證] 頁面
- Microsoft Graph 許可權位於 [API 許可權] 頁面上，以及其他許可權
- 設定檔在品牌頁面上
- [驗證] 頁面上的 [Advanced option-Live SDK 支援]。

## <a name="application-secretscertificates--secrets"></a>應用程式秘密/憑證 & 秘密

在新的體驗中， **應用程式密碼** 已重新命名為 **憑證 & 秘密**。 此外， **公開金鑰** 稱為「 **憑證** 」，而 **密碼** 則稱為「 **用戶端**密碼」。 基於安全性考慮，我們選擇不要將這項功能納入新的體驗中，因此您無法再產生新的金鑰組。

## <a name="platformsauthentication-reply-urlsredirect-uris"></a>平臺/驗證：回復 Url/重新導向 Uri
在舊版體驗中，應用程式具有 Web、原生和 Web API 的平臺區段，可設定重新導向 Url、登出 URL 和隱含流程。

在新的體驗中，您可以在應用程式的 [驗證] 區段找到 [回復 Url] \' 。 此外，它們也稱為重新導向 Uri，重新導向 Uri 的格式已變更。 它們必須與應用程式類型相關聯 (web 或公用用戶端-行動裝置和桌面) 。 [深入了解](quickstart-register-app.md#add-a-redirect-uri)

Web Api 會在公開 API 頁面中進行設定。

> [!NOTE]
> 試用新的驗證設定體驗，您可以在其中根據您要設為目標的平台或裝置來設定應用程式的設定。 [深入了解](quickstart-register-app.md#configure-platform-settings)

## <a name="microsoft-graph-permissionsapi-permissions"></a>Microsoft Graph 許可權/API 許可權

-   選取舊版體驗中的 API 時，您可以從 Microsoft Graph Api 中選擇。 在新的體驗中，您可以從多個 Microsoft Api 中選擇，包括來自您組織和 Api 的 Microsoft Graph、Api，這會在三個索引標籤中提供： Microsoft Api、我的組織使用的 Api，或我的 Api。 [我的組織] 上的搜尋列會透過租使用者中的服務主體，使用 tab 鍵搜尋。

    > [!NOTE]
    > 如果您的應用程式未與租使用者相關聯，您就不會看到此索引標籤。 如需如何使用新體驗來要求許可權的詳細資訊，請參閱 [本快速入門](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory/develop/quickstart-configure-app-access-web-apis.md)。

-   舊的體驗沒有 **[授與許可權** ] 按鈕。 在新的體驗中，有一個授與同意區段，並在應用程式的 [API 許可權] 區段上具有 **[授與系統管理員同意** ] 按鈕。 只有系統管理員可以授與同意，而且只有系統管理員才能啟用此按鈕。 當系統管理員選取 [ **授與系統管理員同意** ] 按鈕時，系統會將系統管理員同意授與所有要求的許可權。

## <a name="profile"></a>設定檔
在舊版體驗中，設定檔具有標誌、首頁 URL、服務條款 URL 及隱私權聲明 URL 設定。 在新的體驗中，您可以在 [商標] 頁面中找到這些功能。

## <a name="application-manifest"></a>應用程式資訊清單
在新的體驗中，資訊清單頁面可讓您編輯和更新應用程式的屬性。 如需詳細資訊，請參閱[應用程式資訊清單](reference-app-manifest.md)。

## <a name="new-ui"></a>新的 UI
先前可以使用資訊清單編輯器或 API 來設定或不存在的屬性有新的 UI。

-   您可以在 [驗證] 頁面上找到隱含授與流程 (oauth2AllowImplicitFlow) 。 不同于舊體驗，您可以啟用存取權杖或識別碼權杖，或兩者。

-   此 API 所定義的範圍 (oauth2Permissions) 和授權的用戶端應用程式 (preAuthorizedApplications) 可透過公開 API 頁面進行設定。 如需如何將應用程式設定為 web API 以及公開許可權/範圍的詳細資訊，請參閱 [本快速入門](quickstart-configure-app-expose-web-apis.md)。

-   在 [ [應用程式] \' 同意提示](application-consent-experience.md) 中顯示給使用者的發行者網域 () 可在 [商標] 頁面上找到。 如需有關如何設定發行者網域的詳細資訊，請參閱 [這個](howto-configure-publisher-domain.md)作法。

## <a name="limitations"></a>限制

新的體驗具有下列限制：

-   新的體驗尚不支援 Azure AD B2C 租使用者應用程式註冊。

-   新的體驗尚不支援使用個人 Microsoft 帳戶建立 Live SDK 應用程式。

-   在 UI 中不支援變更支援帳戶的值。 除非您在 \' Azure AD 單一租使用者和多租使用者之間重新切換，否則您必須使用應用程式資訊清單。

   > [!NOTE]
   > 如果您是 Azure AD 租使用者中的個人 Microsoft 帳戶使用者，而且租使用者系統管理員有 Azure 入口網站的有限存取權，您可能會收到拒絕存取權。 但是，如果您在搜尋列中輸入應用程式註冊，或將其釘選，就可以存取新的體驗。

## <a name="next-steps"></a>接下來的步驟

若要開始使用新的應用程式註冊體驗，請參閱 [快速入門：使用 Microsoft 身分識別平臺註冊應用程式](quickstart-register-app.md)。
