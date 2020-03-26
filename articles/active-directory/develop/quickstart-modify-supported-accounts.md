---
title: 修改 Microsoft 身分識別平台應用程式帳戶 | Azure
description: 設定使用 Microsoft 身分識別平台所註冊的應用程式，以變更可以存取應用程式的使用者或帳戶。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 05/08/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: aragra, lenalepa, sureshja
ms.openlocfilehash: 94fed6f4aa62c7e649cf7d644e571b30561e0da4
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "80050233"
---
# <a name="quickstart-modify-the-accounts-supported-by-an-application"></a>快速入門：修改應用程式所支援的帳戶

在 Microsoft 身分識別平台註冊應用程式時，您可以只讓組織中的使用者存取您的應用程式。 或者，您可能也會想讓應用程式可供外部組織的使用者、外部組織的使用者，以及不一定屬於組織的使用者 (個人帳戶) 存取。

在本快速入門中，您會了解如何修改應用程式的設定，以變更可以存取應用程式的使用者或帳戶。

## <a name="prerequisites"></a>Prerequisites

開始之前，請先確定您已完成以下先決條件：

* 了解所支援的[權限和同意](v2-permissions-and-consent.md)，在建置其他使用者或應用程式需要使用的應用程式時，這些是務必了解的重要資訊。
* 擁有已有應用程式在其中註冊的租用戶。
  * 如果您尚未註冊應用程式，請[了解如何使用 Microsoft 身分識別平台來註冊應用程式](quickstart-register-app.md)。

## <a name="sign-in-to-the-azure-portal-and-select-the-app"></a>登入 Azure 入口網站，然後選取應用程式

請先遵循下列步驟，然後才能設定應用程式：

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 如果您的帳戶可讓您存取多個租用戶，請在右上角選取帳戶，然後將您的入口網站工作階段設定為想要的 Azure AD 租用戶。
1. 在左側導覽窗格中，選取 [Azure Active Directory]  服務，然後選取 [應用程式註冊]  。
1. 尋找並選取您要設定的應用程式。 在選取應用程式後，您會看到應用程式的 [概觀]  或主要註冊頁面。
1. 遵循相關步驟來[變更應用程式註冊以支援不同的帳戶](#change-the-application-registration-to-support-different-accounts)。
1. 如果您有單頁應用程式，請[啟用 OAuth 2.0 隱含授與](#enable-oauth-20-implicit-grant-for-single-page-applications)。

## <a name="change-the-application-registration-to-support-different-accounts"></a>變更應用程式註冊以支援不同的帳戶

如果您正在撰寫想要提供給組織外的客戶或合作夥伴使用的應用程式，您必須在 Azure 入口網站中更新應用程式定義。

> [!IMPORTANT]
> Azure AD 會要求多租用戶應用程式的應用程式識別碼 URI 必須是全域唯一的。 「應用程式識別碼 URI」是其中一種可在通訊協定訊息中識別應用程式的方式。 在單一租用戶應用程式中，只要該租用戶內有唯一的應用程式識別碼 URI 就已足夠。 就多租用戶應用程式而言，該 URI 則必須具全域唯一性，Azure AD 才能在所有租用戶中找到該應用程式。 系統會透過要求「應用程式識別碼 URI」必須具有與已驗證的 Azure AD 租用戶網域相符的主機名稱，來強制執行全域唯一性。 例如，如果租用戶的名稱是 contoso.onmicrosoft.com，則有效的應用程式識別碼 URI 會是 `https://contoso.onmicrosoft.com/myapp`。 如果租用戶的已驗證網域是 contoso.com，則有效的應用程式識別碼 URI 也會是 `https://contoso.com/myapp`。 如果「應用程式識別碼 URI」沒有按照這個模式，將應用程式設定成多租用戶時就會失敗。

### <a name="to-change-who-can-access-your-application"></a>變更可以存取應用程式的使用者

1. 從應用程式的 [概觀]  頁面，選取 [驗證]  區段，然後變更 [支援的帳戶類型]  底下所選取的值。
    * 如果您要建置企業營運 (LOB) 應用程式，請選取 [只有這個目錄中的帳戶]  。 如果未在目錄中註冊應用程式，則無法使用此選項。
    * 如果您想要鎖定所有商業和教育客戶，請選取 [任何組織目錄中的帳戶]  。
    * 選取 [任何組織目錄中的帳戶及個人的 Microsoft 帳戶]  以鎖定最廣泛的一組客戶。
1. 選取 [儲存]  。

## <a name="enable-oauth-20-implicit-grant-for-single-page-applications"></a>啟用單頁應用程式的 OAuth 2.0 隱含授與

單頁應用程式 (SPA) 的結構，通常會設計成具有在瀏覽器中執行且使用大量 JavaScript 的前端，此前端會呼叫應用程式的 Web API 後端以執行其商務邏輯。 針對裝載於 Azure AD 中的 SPA，您會使用 OAuth 2.0 隱含授權驗證具備 Azure AD 的使用者，並取得您可以使用的權杖以保護從應用程式的 JavaScript 用戶端到其後端 web API 的呼叫。

使用者授與同意權之後，這個相同的驗證通訊協定可用來取得權杖以保護用戶端和其他為應用程式設定之 web API 資源之間的呼叫。 若要深入了解隱含授權授與，並協助您決定其是否適合您的應用程式案例，請了解 Azure AD [v1.0](../azuread-dev/v1-oauth2-implicit-grant-flow.md) 和 [v2.0](v2-oauth2-implicit-grant-flow.md) 中的 OAuth 2.0 隱含授與流程。

根據預設，應用程式的 OAuth 2.0 隱含授與會停用。 您可以藉由遵循下面所述步驟，來啟用應用程式的 OAuth 2.0 隱含授與。

### <a name="to-enable-oauth-20-implicit-grant"></a>啟用 OAuth 2.0 隱含授權

1. 從應用程式的 [概觀]  頁面，選取 [驗證]  區段。
1. 在 [進階設定]  底下，找到 [隱含授與]  區段。
1. 選取 [識別碼權杖]  和 (或) [存取權杖]  。
1. 選取 [儲存]  。

## <a name="next-steps"></a>後續步驟

了解與應用程式相關的下列其他應用程式管理快速入門：

* [使用 Microsoft 身分識別平台來註冊應用程式](quickstart-register-app.md)
* [設定用戶端應用程式以存取 Web API](quickstart-configure-app-access-web-apis.md)
* [設定應用程式以公開 Web API](quickstart-configure-app-expose-web-apis.md)
* [移除使用 Microsoft 身分識別平台所註冊的應用程式](quickstart-remove-app.md)

若要了解代表已註冊的應用程式和它們之間關係的兩個 Azure AD 物件，請參閱[應用程式物件和服務主體物件](app-objects-and-service-principals.md)。

若要了解使用 Azure Active Directory 開發應用程式時應使用的商標指導方針，請參閱[應用程式的商標指導方針](howto-add-branding-in-azure-ad-apps.md)。
