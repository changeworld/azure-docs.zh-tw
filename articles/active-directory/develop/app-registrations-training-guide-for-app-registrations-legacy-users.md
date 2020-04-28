---
title: 新版 Azure 入口網站應用程式註冊訓練指南
description: 引進新的 Azure 入口網站應用程式註冊體驗
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: marsma
ms.reviewer: lenalepa, keyam
ms.custom: aaddev
ms.openlocfilehash: a437d54dac50be7ddaad899a1cf0a3e93aade8f5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80154571"
---
# <a name="new-azure-portal-app-registration-training-guide"></a>新版 Azure 入口網站應用程式註冊訓練指南

您可以在 Azure 入口網站的新[應用程式註冊](https://go.microsoft.com/fwlink/?linkid=2083908)體驗中找到許多改良功能。 如果您熟悉 Azure 入口網站中的應用程式註冊（舊版）體驗，請使用此訓練指南來開始使用新的體驗。

在 Azure Active Directory 中，這裡所述的新應用程式註冊體驗已正式推出（GA）。 在 Azure Active Directory B2C （Azure AD B2C）中，這項體驗目前為預覽狀態。

## <a name="key-changes"></a>金鑰變更

- 應用程式註冊不限於*web 應用程式/API*或*原生*應用程式。 您可以藉由註冊個別的重新導向 Uri，為所有應用程式使用相同的應用程式註冊。

- 舊版體驗僅支援使用組織（Azure AD）帳戶登入的應用程式。 應用程式已註冊為單一租使用者。 應用程式僅支援應用程式註冊所在目錄中的組織帳戶。 應用程式可以修改為多租使用者，並支援所有的組織帳戶。 新的體驗可讓您註冊可以同時支援這兩個選項的應用程式，以及第三個選項：所有組織帳戶，以及個人 Microsoft 帳戶。

- 只有在使用組織帳戶登入 Azure 入口網站時，才可以使用舊版體驗。 有了新的體驗，您可以使用未與目錄相關聯的個人 Microsoft 帳戶。

## <a name="list-of-applications"></a>應用程式清單

新的應用程式清單會顯示透過 Azure 入口網站中的繼承應用程式註冊體驗來註冊的應用程式。 這些應用程式會使用 Azure AD 帳戶來登入。 [新增應用程式] 清單也會顯示透過應用程式註冊入口網站註冊的應用程式。 這些應用程式會使用 Azure AD 和個人 Microsoft 帳戶登入。

>[!NOTE]
>應用程式註冊入口網站已被取代。

新的應用程式清單沒有 [**應用程式類型**] 資料行，因為單一應用程式註冊可以是數種類型。 此清單有兩個額外的資料行： [**建立于**] 和 [**憑證 & 密碼**]。 [憑證 **& 密碼**] 會顯示已在應用程式上註冊的認證狀態。 狀態包括**Current**[目前 **]、[即將到期]** 和 [已**到期**]。

## <a name="new-app-registration"></a>新增應用程式註冊

在舊版體驗中，若要註冊應用程式，您必須提供：**名稱**、**應用程式類型**和登**入 URL/重新導向 URI**。 建立的應用程式只 Azure AD 單一租使用者應用程式。 他們只支援從已註冊應用程式的目錄中的組織帳戶。

在新的體驗中，您必須提供應用程式的**名稱**，並選擇**支援的帳戶類型**。 您可以選擇性地提供重新**導向 URI**。 如果您提供 [重新導向 URI]，則必須指定它是 web/公用（行動裝置和桌面）。 如需詳細資訊，請參閱[快速入門：使用 Microsoft 身分識別平臺註冊應用程式](quickstart-register-app.md)。 如 Azure AD B2C，請參閱[在 Azure Active Directory B2C 中註冊應用程式](../../active-directory-b2c/tutorial-register-applications.md)。

## <a name="differences-between-the-application-registration-portal-and-app-registrations-page"></a>應用程式註冊入口網站與應用程式註冊頁面之間的差異

### <a name="the-legacy-properties-page"></a>舊版屬性頁面

舊版體驗具有 [**屬性**] 頁面。 **屬性**具有下欄欄位：

- **名稱**
- **物件識別碼**
- **應用程式識別碼**
- **應用程式識別碼 URI**
- **標誌**
- **首頁 URL**
- **登出 URL**
- **服務條款 URL**
- **隱私權聲明 URL**
- **應用程式類型**
- **多租使用者**

新的體驗沒有該頁面。 您可以在這裡找到對等的功能：

- [**名稱**]、[**標誌**]、[**首頁 url**]、[**服務條款 url**] 和 [**隱私權聲明 url** ] 現在位於應用程式的 [**商標**] 頁面上。
- **物件識別碼**和**應用程式（用戶端）識別碼**位於 [**總覽**] 頁面上。
- 舊版體驗中的**多租**使用者切換所控制的功能，已由 [**驗證**] 頁面上**受支援的帳戶類型**所取代。 如需詳細資訊，請參閱[快速入門：修改應用程式所支援的帳戶](quickstart-modify-supported-accounts.md)。
- [**登出 URL** ] 現在位於 [**驗證**] 頁面上。
- **應用程式類型**不再是有效的欄位。 相反地，您可以在 [**驗證**] 頁面上找到的重新導向 uri，判斷支援哪些應用程式類型。
- **APP ID uri**現在稱為**應用程式識別碼 uri** ，您可以在**公開 API**中找到它。 在舊版體驗中，此屬性是使用下列格式 autoregistered 的： `https://{tenantdomain}/{appID}`，例如`https://microsoft.onmicrosoft.com/492439af-3282-44c3-b297-45463339544b`。 在新的體驗中，它會自動`api://{appID}`產生為，但必須加以明確儲存。 在 Azure AD B2C 租使用者中`https://{tenantdomain}/{appID}` ，仍然會使用格式。

### <a name="reply-urlsredirect-urls"></a>回復 Url/重新導向 Url

在舊版體驗中，應用程式有 [**回復 url** ] 頁面。 在新體驗中，回復 Url 可以在應用程式的**驗證**頁面上找到。 它們現在稱為「重新**導向 uri**」。

重新導向 Uri 的格式已變更。 它們必須與應用程式類型（web 或公用）相關聯。 基於安全性理由，除了以外`http://` ，不支援萬用字元和配置*http://localhost*。

### <a name="keyscertificates--secrets"></a>金鑰/憑證 & 秘密

在舊版體驗中，應用程式有 [**金鑰**] 頁面。 在新的體驗中，已將其重新命名為 **& 秘密的憑證**。

**公開金鑰**現在稱為**憑證**。 **密碼**現在稱為「**用戶端**密碼」。

### <a name="required-permissionsapi-permissions"></a>必要許可權/API 許可權

在舊版體驗中，應用程式具有 [**必要許可權**] 頁面。 在新的體驗中，它已重新命名為**API 許可權**。

當您選取舊版體驗中的 API 時，您可以從一小部分的 Microsoft Api 中進行選擇。 您也可以搜尋租使用者中的服務主體。 在新體驗中，您可以從多個索引標籤中選擇： **Microsoft api**、**我的組織使用的 Api**，或**我的 api**。 **我組織的 api**上的搜尋列會使用索引標籤搜尋租使用者中的服務主體。

> [!NOTE]
> 如果您的應用程式未與租使用者建立關聯，您將不會看到此索引標籤。 如需如何要求許可權的詳細資訊，請參閱[快速入門：設定用戶端應用程式以存取 Web api](quickstart-configure-app-access-web-apis.md)。

舊版體驗具有 [**要求的許可權**] 頁面頂端的 **[授與許可權**] 按鈕。 在新的體驗中，[**授與同意**] 頁面在應用程式的 [ **API 許可權**] 區段上具有 [授與系統**管理員同意**] 按鈕。 按鈕的運作方式也有一些差異。

在舊版體驗中，邏輯會根據登入的使用者和所要求的許可權而有所不同。 邏輯是：

- 如果只要求使用者同意的許可權，而且登入的使用者不是系統管理員，則使用者可以將要求的許可權授與使用者同意。
- 如果至少有一個許可權需要系統管理員同意，而且登入的使用者不是系統管理員，則使用者在嘗試授與同意時收到錯誤。
- 如果登入的使用者是系統管理員，則會針對所有要求的許可權授與系統管理員同意。

在新的體驗中，只有系統管理員可以授與同意。 當系統管理員選取 **[授與系統管理員同意**] 時，系統管理員同意會授與所有要求的許可權。

## <a name="deleting-an-app-registration"></a>刪除應用程式註冊

在舊版體驗中，您只能刪除單一租使用者應用程式。 多租使用者應用程式的 [刪除] 按鈕已停用。 在新的體驗中，您可以刪除處於任何狀態的應用程式，但必須確認該動作。 如需詳細資訊，請參閱[快速入門：移除向 Microsoft 身分識別平臺註冊的應用程式](quickstart-remove-app.md)。

## <a name="application-manifest"></a>應用程式資訊清單

舊版和新體驗會在資訊清單編輯器中使用不同版本的 JSON 格式。 如需詳細資訊，請參閱[Azure Active Directory 應用程式資訊清單](reference-app-manifest.md)。

## <a name="new-ui"></a>新增 UI

新體驗會加入下列屬性的 UI 控制項：

- [**驗證**] 頁面具有隱含的授`oauth2AllowImplicitFlow`與**流程**（）。 與舊版體驗不同的是，您可以啟用**存取權杖**或**識別碼權杖**，或兩者。
- [**公開 API** ] 頁面包含**此 api** （`oauth2Permissions`）所定義的範圍，以及已授權`preAuthorizedApplications`的**用戶端應用程式**（）。 如需有關如何將應用程式設定為 Web API 並公開許可權/範圍的詳細資訊，請參閱[快速入門：設定應用程式以公開 Web api](quickstart-configure-app-expose-web-apis.md)。
- [**商標**] 頁面包含**發行者網域**。 發行者網域會在[應用程式的同意提示](application-consent-experience.md)中向使用者顯示。 如需詳細資訊，請參閱[如何：設定應用程式的發行者網域](howto-configure-publisher-domain.md)。

## <a name="limitations"></a>限制

新的體驗有下列限制：

- 用戶端秘密（應用程式密碼）的格式與舊版體驗不同，可能會中斷 CLI。
- 在 UI 中不支援變更所支援帳戶的值。 您必須使用應用程式資訊清單，除非您要在 Azure AD 單一租使用者和多租使用者之間切換。
