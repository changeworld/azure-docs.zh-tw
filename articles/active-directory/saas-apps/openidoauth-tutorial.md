---
title: 從 Azure AD 應用程式資源庫設定 OpenID/OAuth 應用程式 | Microsoft Docs
description: 從 Azure AD 應用程式資源庫設定 OpenID/OAuth 應用程式的步驟。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/30/2019
ms.author: jeedes
ms.custom: has-adal-ref
ms.openlocfilehash: ce4cccba678a934780dc987f1441ea658a70f8eb
ms.sourcegitcommit: 83610f637914f09d2a87b98ae7a6ae92122a02f1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2020
ms.locfileid: "91996610"
---
# <a name="configure-an-openidoauth-application-from-the-azure-ad-app-gallery"></a>從 Azure AD 應用程式資源庫設定 OpenID/OAuth 應用程式

## <a name="process-of-adding-an-openid-application-from-the-gallery"></a>從資源庫新增 OpenID 應用程式的程序

1. 在 [Azure 入口網站](https://portal.azure.com)的左側窗格中，選取 [Azure Active Directory]  。

    ![Azure Active Directory 按鈕](common/select-azuread.png))

2. 移至 [企業應用程式]   > [所有應用程式]  。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 選取對話方塊頂端的 [新增應用程式]  。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中，輸入應用程式名稱。 從結果面板中選取所需的應用程式，然後向應用程式註冊。

    ![結果清單中的 Openid](common/search-new-app.png)

    > [!NOTE]
    > 對於 OpenID Connect 和 OAuth 應用程式，預設會停用 [新增]  按鈕。 租用戶系統管理員應該選取 [註冊] 按鈕並為該應用程式提供同意。 然後，應用程式就會新增至客戶租用戶，您可以在其中進行設定。 您不需要明確地新增應用程式。

    ![[新增] 按鈕](./media/openidoauth-tutorial/addbutton.png)

5. 當您選取 [註冊] 連結時，系統會將您重新導向至 Azure Active Directory (Azure AD) 頁面以進行登入認證。

6. 驗證成功之後，您必須接受同意頁面上的同意。 在那之後，應用程式首頁會隨即出現。

    > [!NOTE]
    > 您只可以新增一個應用程式執行個體。 如果您已經新增一個並嘗試再次提供同意，系統將不會再次將它新增至租用戶。 因此，邏輯上您只能在租用戶中使用一個應用程式執行個體。

## <a name="authentication-flow-using-openid-connect"></a>使用 OpenID Connect 驗證流程

最基本的登入流程包含下列步驟：

![使用 OpenID Connect 驗證流程](./media/openidoauth-tutorial/authenticationflow.png)

### <a name="multitenant-application"></a>多租用戶應用程式
多租用戶應用程式可供許多組織使用，而不限單一組織。 這些通常是由獨立軟體廠商 (ISV) 撰寫的軟體即服務 (SaaS) 應用程式。

每個要使用多租用戶應用程式的目錄中，都須佈建多租用戶應用程式。 這些應用程式需要使用者或系統管理員同意才能加以註冊。 當應用程式目錄中已在目錄中註冊並獲權存取 Graph API 或其他 Web API 時，此同意程序會啟動。 當不同組織的使用者或系統管理員註冊來使用應用程式時，對話方塊會顯示應用程式需要的權限。

然後，使用者或系統管理員可以對應用程式表示同意。 同意後即可讓應用程式存取所述的資料，而最終目的是在目錄中註冊應用程式。

> [!NOTE]
> 如果要讓您的應用程式可供多個目錄中的使用者使用，則需要有機制來判斷使用者所在的租用戶。 單一租用戶應用程式只須查看其本身目錄的使用者。 多租用戶應用程式需要從 Azure AD 的所有目錄中識別特定使用者。
>
> 為了完成這項工作，Azure AD 提供一個共同驗證端點，供任何多租用戶應用程式引導登入要求，而非提供租用戶特定的端點。 此端點是 `https://login.microsoftonline.com/common`，適用於 Azure AD 中的所有目錄。 租用戶專屬端點可能是 `https://login.microsoftonline.com/contoso.onmicrosoft.com`。
>
> 常用端點是您開發應用程式時必須納入考量的重點。 您將需要必要邏輯來處理多租用戶的登入、登出和權杖驗證。

根據預設，Azure AD 會推廣多租用戶應用程式。 因為多租用戶應用程式可在組織間輕鬆存取，而且在您接受同意之後即可輕易上手。

## <a name="consent-framework"></a>同意架構

您可以使用 Azure AD 同意架構來開發多租用戶的 Web 應用程式和原生用戶端應用程式。 這些應用程式允許與應用程式註冊所在的租用戶不同之 Azure AD 租用戶的使用者帳戶登入。 應用程式可能也需要存取 Web API，例如：
- Microsoft Graph API，以便存取 Azure AD、Intune 和 Microsoft 365 中的服務。
- 其他 Microsoft 服務的 API。
- 您自己的 Web API。

此架構以使用者或系統管理員為根據，他們可同意讓應用程式在他們的目錄中註冊。 註冊可能包含存取目錄資料。 取得同意之後，用戶端應用程式將能代表使用者呼叫 Microsoft Graph API，並視需要使用資訊。

[Microsoft Graph API](https://developer.microsoft.com/graph/) 會提供 Microsoft 365 中資料的存取權，例如：

- Exchange 中的行事曆和訊息。
- SharePoint 中的網站和清單。
- OneDrive 中的文件。
- OneNote 中的 Notebook。
- Planner 中的工作。
- Excel 中的活頁簿。

Graph API 也會提供 Azure AD 中的使用者和群組存取權，以及多個 Microsoft 雲端服務中的其他資料物件存取權。

下列步驟會示範如何將同意體驗用於應用程式開發人員和使用者：

1. 假設您擁有一個 Web 用戶端應用程式，其需要要求特定權限以存取資源或 API。 Azure 入口網站會在設定期間用來宣告權限要求。 就和其他組態設定一樣，它們會成為應用程式之 Azure AD 註冊的一部分。 如需權限要求路徑，您需要遵循下列步驟：

    a. 按一下左側功能表中的 [應用程式註冊]  ，並在搜尋方塊中輸入應用程式名稱以開啟您的應用程式。

    ![顯示已從左側功能表中選取 [應用程式註冊]，並已反白顯示 [應用程式識別碼] 搜尋方塊的螢幕擷取畫面。](./media/openidoauth-tutorial/application.png)

    b. 按一下 [檢視 API 權限]  。

    ![顯示 [呼叫 A P I] 頁面的螢幕擷取畫面，其中已選取 [檢視 A P I 權限] 按鈕。](./media/openidoauth-tutorial/api-permission.png)

    c. 按一下 [新增權限]  。

    ![顯示 [A P I 權限] 區段的螢幕擷取畫面，其中已選取 [新增權限] 按鈕。](./media/openidoauth-tutorial/add-permission.png)

    d. 按一下 [Microsoft Graph]  。

    ![顯示 [要求 A P I 權限] 頁面的螢幕擷取畫面，其中包含 [Microsoft A P I] 索引標籤，並已選取 [Microsoft Graph] 圖格。](./media/openidoauth-tutorial/microsoft-graph.png)

    e. 從 [委派權限]  和 [應用程式權限]  中選取所需的選項。

    ![圖形 API](./media/openidoauth-tutorial/graphapi.png)

2. 由於應用程式的權限已更新。 當應用程式執行時，使用者即將第一次使用此應用程式。 首先，應用程式必須從 Azure AD /授權端點取得授權碼。 授權碼接著可用來取得新的存取權和重新整理權杖。

3. 如果使用者尚未經過驗證，Azure AD /授權端點就會提示使用者登入。

    ![帳戶登入提示的螢幕擷取畫面](./media/openidoauth-tutorial/authentication.png)

4. 使用者登入之後，Azure AD 會判斷是否需要向使用者顯示同意頁面。 此判斷根據使用者 (或其組織的系統管理員) 是否已經同意應用程式。

   如果尚未授與同意，Azure AD 會提示使用者取得同意，並顯示其運作所需的必要權限。 同意對話方塊中顯示的權限，會和在 Azure 入口網站 [委派的權限] 中選取的權限相符。

    ![同意頁面](./media/openidoauth-tutorial/consentpage.png)

一般使用者可以同意某些權限。 其他權限則需要租用戶系統管理員的同意。

## <a name="difference-between-admin-consent-and-user-consent"></a>系統管理員同意和使用者同意之間的差異

身為系統管理員，您也可以代表租用戶中的所有使用者，同意應用程式的委派權限。 系統管理員同意不會讓租用戶中的每個使用者看到同意對話方塊。 具有「系統管理員」角色的使用者可以在 Azure 入口網站中提供同意。 在應用程式的 [設定]  頁面中，選取 [所需的權限]   > [授與管理員同意]  。

![授與權限按鈕](./media/openidoauth-tutorial/grantpermission.png)

> [!NOTE]
> 使用 ADAL.js 的單頁應用程式 (SPA) 現在必須使用 [授與管理員同意]  按鈕來授與明確的同意。 否則，應用程式會在要求存取權杖時失敗。

僅限應用程式的權限一律需要租用戶系統管理員的同意。 如果您的應用程式要求僅限應用程式的權限，當使用者嘗試登入應用程式時，將會出現錯誤訊息。 訊息會指出該使用者無法同意。

如果您的應用程式使用需要系統管理員同意的權限，您就必須要有相關的表示，例如可供系統管理員開始動作的按鈕或連結。 您的應用程式針對此動作傳送的要求是一個一般的 OAuth2/OpenID Connect 授權要求。 此要求包含 prompt=admin_consent  查詢字串參數。

在系統管理員同意且系統在客戶的租用戶中建立服務主體之後，後續的登入要求就不需要 prompt=admin_consent  參數。 由於系統管理員已決定可接受要求的權限，因此從該時間點之後，就不會再提示租用戶中的任何其他使用者行使同意權。

租用戶系統管理員可以停用一般使用者對應用程式行使同意權的能力。 如果停用這項功能，就一律需要系統管理員同意，才能在租用戶中使用應用程式。 若要在停用使用者同意的情況下測試應用程式，您可以在 [Azure 入口網站](https://portal.azure.com/)中找到設定參數。 其位於 [企業應用程式]  下的[使用者設定](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/)區段。

要求的權限不需要系統管理員同意的應用程式，也可以使用 prompt=admin_consent  參數。 例如，應用程式需要的體驗是，租用戶系統管理員「註冊」一次，此後就不會再提示其他使用者要表示同意。

假設應用程式需要系統管理員同意，而系統管理員登入時不需要傳送 prompt=admin_consent  參數。 當系統管理員順利同意此應用程式時，這只會套用至其使用者帳戶。 一般使用者將仍然無法登入此應用程式或對其行使同意權。 當您想要先讓租用戶系統管理員能夠瀏覽您的應用程式，然後才允許其他使用者存取時，這個功能相當有用。
