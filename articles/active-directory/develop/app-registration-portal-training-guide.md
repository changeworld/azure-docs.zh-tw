---
title: 新版 Azure 入口網站應用程式註冊體驗
titleSuffix: Microsoft identity platform
description: Azure 入口網站中的新應用程式註冊體驗簡介
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
ms.openlocfilehash: 43ed0bfed1dbe48c4f66a820ecad6fedf11115fe
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85478156"
---
# <a name="the-new-azure-portal-app-registration-experience"></a>新的 Azure 入口網站應用程式註冊體驗

Azure 入口網站的新[應用程式註冊](https://go.microsoft.com/fwlink/?linkid=2083908)體驗中有許多改進。 如果您更熟悉用來註冊或管理應用程式的應用程式註冊入口網站（apps.dev.microsoft.com）（在這裡稱為「舊體驗」），本指南將協助您開始使用新的體驗。

## <a name="whats-not-changing"></a>什麼不會改變？

- 您的應用程式和相關設定可以在新的體驗中找到。 您不需要再次註冊應用程式，應用程式的使用者也不需要再次登入。

    > [!NOTE]
    > 您必須使用您用來註冊應用程式的帳戶登入，才能在 Azure 入口網站中尋找它們。 我們建議您在 Azure 入口網站中檢查已登入的使用者是否符合已登入應用程式註冊入口網站的使用者，方法是比較您的設定檔中的電子郵件地址。
    >
    > 在某些情況下，特別是當您使用個人 Microsoft 帳戶（例如 Outlook、Live、Xbox 等等）登入 Azure AD 電子郵件地址時，我們發現當您從舊體驗中移至 Azure 入口網站時，它會將您的 Azure AD 租使用者中的電子郵件登入不同的帳戶。 如果您仍然認為應用程式遺失，請登出，然後使用正確的帳戶登入。

- Azure 入口網站中尚不支援使用個人 Microsoft 帳戶建立的 Live SDK 應用程式，且將會在不久的未來繼續保留舊體驗。

## <a name="key-changes"></a>金鑰變更

-   在舊版的體驗中，應用程式預設會註冊為*聚合*式應用程式-支援所有組織帳戶（多租使用者）以及個人 Microsoft 帳戶的應用程式。 這無法透過舊體驗進行修改，因此難以建立僅支援組織帳戶的應用程式（多租使用者或單一租使用者）。
    新的體驗可讓您註冊支援所有這些選項的應用程式。 [深入瞭解應用程式類型](active-directory-v2-registration-portal.md)。

-   在新的體驗中，如果您的個人 Microsoft 帳戶也在 Azure AD 租使用者中，您會看到三個索引標籤--租使用者中的所有應用程式、租使用者中擁有的應用程式，以及來自您個人帳戶的應用程式。 因此，如果您認為已向個人 Microsoft 帳戶註冊的應用程式遺失，請**從您的 [個人帳戶**] 索引標籤檢查應用程式。

-   在新體驗中，您可以流覽至您的設定檔並選擇 [切換目錄]，輕鬆地在租使用者之間切換。

## <a name="list-of-applications"></a>應用程式清單

-   [新增應用程式] 清單會顯示透過 Azure 入口網站中的繼承應用程式註冊體驗（僅限登入 Azure AD 帳戶的應用程式），以及應用[程式註冊入口網站](https://apps.dev.microsoft.com/)（同時登入 Azure AD 和個人 Microsoft 帳戶的應用程式）登錄的應用程式。

-   新的應用程式清單有兩個額外的資料行： [在資料行和憑證**上建立**] **& [秘密**] 資料行，該資料行會顯示已在應用程式上註冊之認證的狀態（目前、即將過期或已過期）。

## <a name="new-app-registration"></a>新增應用程式註冊

在舊體驗中，若要註冊應用程式，您只需要提供名稱。 已建立的應用程式已註冊為*聚合*式應用程式-支援所有組織目錄（多租使用者）以及個人 Microsoft 帳戶的應用程式。  這無法透過舊體驗進行修改，因此難以建立僅支援組織帳戶（單一或多租使用者）的應用程式。 [深入瞭解支援的帳戶類型](v2-supported-account-types.md)

在新的體驗中，您必須提供應用程式的名稱，並選擇支援的帳戶類型。 您可以選擇性地提供重新導向 URI。
如果您提供 [重新導向 URI]，則必須指定它是否為 web/公用（原生/行動裝置和桌面）。 如需如何使用新的應用程式註冊體驗來註冊應用程式的詳細資訊，請參閱[此快速入門](quickstart-register-app.md)。

## <a name="app-management-page"></a>應用程式管理頁面

舊版體驗具有應用程式的單一應用程式管理頁面，其中包含下列各節：屬性、應用程式密碼、平臺、擁有者、Microsoft Graph 許可權、設定檔和 Advanced 選項。

Azure 入口網站中的新體驗會在不同的頁面中顯示這些功能。 您可以在這裡找到對等的功能：

- 屬性-[名稱] 和 [應用程式識別碼] 位於 [總覽] 頁面。
- 應用程式秘密位於 [憑證 & 秘密] 頁面
- [驗證] 頁面上的 [平臺設定]
- [API 許可權] 頁面上的 Microsoft Graph 許可權和其他許可權
- [個人資料] 位於 [商標] 頁面
- Advanced option-Live SDK 支援位於 [驗證] 頁面。

## <a name="application-secretscertificates--secrets"></a>應用程式秘密/憑證 & 秘密

在新的體驗中，**應用程式密碼**已重新命名為**憑證 & 密碼**。 此外，**公開金鑰**稱為「**憑證**」，而「**密碼**」則稱為「**用戶端**密碼」。 基於安全性理由，我們選擇不要將此功能帶入新的體驗中，因此，您無法再產生新的金鑰組。

## <a name="platformsauthentication-reply-urlsredirect-uris"></a>平臺/驗證：回復 Url/重新導向 Uri
在舊版的體驗中，應用程式具有 Web、原生和 Web API 的平臺區段，可設定重新導向 Url、登出 URL 和隱含流程。

在新的體驗中，可以在應用程式的 \' 驗證一節找到回復 url。 此外，它們也稱為重新導向 Uri，而重新導向 Uri 的格式也已變更。 它們必須與應用程式類型（web 或公用用戶端-行動裝置和桌上型電腦）相關聯。 [深入了解](quickstart-configure-app-access-web-apis.md#add-redirect-uris-to-your-application)

Web Api 會在 [公開 API] 頁面中設定。

> [!NOTE]
> 試用新的驗證設定體驗，您可以在其中根據您要設為目標的平台或裝置來設定應用程式的設定。 [深入了解](quickstart-configure-app-access-web-apis.md#configure-platform-settings-for-your-application)

## <a name="microsoft-graph-permissionsapi-permissions"></a>Microsoft Graph 許可權/API 許可權

-   選取舊版體驗中的 API 時，您可以選擇僅 Microsoft Graph Api。 在新的體驗中，您可以選擇許多 Microsoft Api，包括來自組織和 Api 的 Microsoft Graph、Api，這會顯示在三個索引標籤中： Microsoft Api、我的組織使用的 Api，或我的 Api。 我組織的 Api 上的搜尋列會使用索引標籤搜尋租使用者中的服務主體。

    > [!NOTE]
    > 如果您的應用程式未與租使用者建立關聯，您將不會看到此索引標籤。 如需如何使用新的體驗來要求許可權的詳細資訊，請參閱[此快速入門](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory/develop/quickstart-configure-app-access-web-apis.md)。

-   舊體驗沒有 **[授與許可權**] 按鈕。 在新的體驗中，有一個 [授與同意] 區段，其中包含應用程式的 [API 許可權] 區段上的 **[授與管理員同意**] 按鈕。 只有系統管理員可以授與同意，且此按鈕僅針對管理員啟用。 當系統管理員選取 [**授與系統管理員同意**] 按鈕時，系統管理員同意會授與所有要求的許可權。

## <a name="profile"></a>設定檔
在舊體驗中，設定檔具有標誌、首頁 URL、服務條款 URL 及隱私權聲明 URL 設定。 在新的體驗中，您可以在商標頁面中找到這些功能。

## <a name="application-manifest"></a>應用程式資訊清單
在新體驗中，資訊清單頁面可讓您編輯和更新應用程式的屬性。 如需詳細資訊，請參閱[應用程式資訊清單](reference-app-manifest.md)。

## <a name="new-ui"></a>新增 UI
先前只能使用資訊清單編輯器或 API 來設定屬性的新 UI，或不存在。

-   您可以在 [驗證] 頁面上找到隱含授與流程（oauth2AllowImplicitFlow）。 不同于舊體驗，您可以啟用存取權杖或識別碼權杖，或兩者。

-   此 API 定義的範圍（oauth2Permissions）和授權的用戶端應用程式（preAuthorizedApplications）可以透過 [公開 API] 頁面來設定。 如需有關如何將應用程式設定為 Web API 並公開許可權/範圍的詳細資訊，請參閱[此快速入門](quickstart-configure-app-expose-web-apis.md)。

-   發行者網域（向使用者顯示在[應用程式的 \' 同意提示](application-consent-experience.md)中）可以在商標頁面上找到。 如需有關如何設定發行者網域的詳細資訊，請參閱[此](howto-configure-publisher-domain.md)作法。

## <a name="limitations"></a>限制

新的體驗有下列限制：

-   新的體驗尚不支援 Azure AD B2C 租使用者的應用程式註冊。

-   新的體驗尚不支援以個人 Microsoft 帳戶建立的 Live SDK 應用程式。

-   在 UI 中不支援變更所支援帳戶的值。 您必須使用應用程式資訊清單，除非您在 \' Azure AD 單一租使用者和多租使用者之間切換。

   > [!NOTE]
   > 如果您是 Azure AD 租使用者中的個人 Microsoft 帳戶使用者，而且租使用者系統管理員已限制 Azure 入口網站的存取權，您可能會收到拒絕存取的許可權。 不過，如果您藉由在搜尋列中輸入應用程式註冊來流覽快捷方式，或將它釘選，您就能夠存取新的體驗。

## <a name="next-steps"></a>後續步驟

若要開始使用新的應用程式註冊體驗，請參閱[快速入門：使用 Microsoft 身分識別平臺註冊應用程式](quickstart-register-app.md)。
