---
title: 找出 & 在我的應用程式入口網站上使用應用程式-Azure AD
description: 瞭解如何尋找我的應用程式入口網站，以及如何存取貴組織的雲端式應用程式。
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: curtand
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: 8eb59eebd29c2cc94e20b71b467866686deec2f1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81869715"
---
# <a name="sign-in-and-start-apps-from-the-my-apps-portal"></a>從我的應用程式入口網站登入和啟動應用程式

您可以使用公司或學校帳戶搭配 web 型**我的應用程式**入口網站來執行下列動作：

- 查看和啟動許多組織的雲端式應用程式
- 更新您的設定檔和帳戶資訊
- 查看您的**群組**資訊
- 針對您的應用程式和群組執行**存取權審查**

如果您沒有 [我的應用程式]**** 入口網站的存取權，請連絡技術服務人員以取得權限。

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-my-apps-portal.md)]

> [!Important]
> 本內容適用於我的應用程式使用者。 如果您是系統管理員，可以在[應用程式管理文件](https://docs.microsoft.com/azure/active-directory/manage-apps)中找到更多關於如何設定和管理雲端式應用程式的資訊。

## <a name="supported-browsers"></a>支援的瀏覽器

您可以從下列任何網頁瀏覽器進入**我的應用程式**入口網站：

- Google Chrome

- Mozilla Firefox，26.0 版或更新版本

- Microsoft Edge

- Internet Explorer 第11版（有限支援）

## <a name="download-and-install-the-my-apps-secure-sign-in-extension"></a>下載並安裝我的應用程式安全登入延伸模組

如果系統提示您，請下載並安裝我的應用程式安全登入延伸模組。 此延伸模組可協助您啟動貴組織的任何雲端應用程式，並要求您使用單一登入進程。 如果您的組織已將您設定為單一登入，則會自動安裝此延伸模組，您可以略過本節。

此延伸模組可協助您：

- 從登入頁面直接登入應用程式。

- 使用**快速搜尋**功能啟動任何應用程式。

- 請參閱您在**最近使用**的一節中所使用的最後一個應用程式。

- 在遠端搭配[應用程式 Proxy](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-get-started)使用內部公司 url。

### <a name="to-download-and-install-the-extension"></a>下載並安裝擴充功能

根據您所使用的瀏覽器，下載並安裝延伸模組：

- **Google Chrome。** 從 Chrome Web Store，移至 [[我的應用程式安全登入延伸](https://chrome.google.com/webstore/detail/my-apps-secure-sign-in-ex/ggjhpefgjjfobnfoldnjipclpcfbgbhl)模組] 功能，然後選取 [**新增至 Chrome**]。

- **Mozilla Firefox**在 [ **Firefox 附加**元件] 頁面上，移至 [[我的應用程式安全登入延伸](https://addons.mozilla.org/firefox/addon/access-panel-extension/)模組] 功能，然後選取 [**新增至 Firefox**]。

- **Microsoft Edge**從 [Microsoft Store] 中，移至 [[我的應用程式 Secure 登入延伸](https://www.microsoft.com/p/my-apps-secure-sign-in-extension/9pc9sckkzk84?rtc=1&activetab=pivot%3Aoverviewtab)模組] 功能，然後選取 [**取得**] 以取得 Microsoft Edge 舊版瀏覽器的延伸模組。  
如果您使用新的 Microsoft Edge 瀏覽器（Edge on Chromium），則可以從[Microsoft Edge 附加元件 Store](https://microsoftedge.microsoft.com/addons/category/EdgeExtensionsEditorsPick)取得 extenion。

您的**網址**列右側會加入一個圖示，讓您可以登入並自訂擴充功能。

## <a name="to-change-your-my-apps-portal-using-the-extension"></a>使用延伸模組來變更您的我的應用程式入口網站

您可以在 [**最近使用**的] 區段中選擇要查看的應用程式數目，並決定是否要允許您組織的內部 url 重新導向。

1. 選取**網址**列右邊的新**我的應用程式安全登入延伸** ![模組](media/my-apps-portal/my-apps-portal-extension-icon.png)圖示延伸模組圖示，然後選取 [登**入以開始**使用]。

2. 以滑鼠右鍵按一下**設定**圖示![設定圖示](media/my-apps-portal/my-apps-portal-extension-settings-icon.png)，然後選取 [**設定**]。

3. 在 [**設定**] 方塊中，選取您想要在入口網站上看到的最近使用的應用程式數目，以及是否允許重新導向組織的內部 url，讓您可以從遠端使用它們。

    ![擴充功能的 [設定] 頁面，其中顯示可用的自訂專案](media/my-apps-portal/my-apps-portal-extension-settings-page.png)

## <a name="access-and-use-the-my-apps-portal-by-device"></a>依裝置存取和使用我的應用程式入口網站

您可以從受 Intune 管理的瀏覽器，或從 iOS 或 Android 行動裝置存取和使用電腦上的我的應用程式入口網站。

![我的應用程式入口網站中的 [應用程式] 頁面](media/my-apps-portal/my-apps-portal-apps-page.png)

### <a name="access-and-use-the-my-apps-portal-on-your-computer"></a>在您的電腦上存取並使用我的應用程式入口網站

如果您有權存取和使用組織的雲端式應用程式，您可以透過**我的應用程式**入口網站來取得。

1. 在您的電腦上登入您的公司或學校帳戶。

2. 在支援的網頁瀏覽器中，開啟並https://myapps.microsoft.com移至，或使用組織提供的連結（如果他們將您導向至自訂網頁， `https://myapps.microsoft.com/contoso.com`例如）。

    [**應用程式**] 頁面隨即出現，其中顯示貴組織所擁有可供您使用的所有雲端應用程式。

3. 從 [**應用程式**] 頁面中，選取您想要開始使用的應用程式。

    隨即會開啟應用程式的新頁面，您可以在此登入（如有需要），或開始使用應用程式。

### <a name="access-and-use-the-my-apps-portal-on-an-intune-managed-browser"></a>在受 Intune 管理的瀏覽器上存取和使用我的應用程式入口網站

從 iOS 和 Android 裝置上的 Intune Managed Browser，觀看並使用貴組織的應用程式。

1. 在您的行動裝置上，從 Apple App Store 和 Google Play 商店下載並安裝 Intune Managed Browser 應用程式。

2. 開啟 Intune Managed Browser 應用程式，移至https://myapps.microsoft.com，或使用組織提供的連結（如果他們將您導向至自訂頁面，例如） https://myapps.microsoft.com/contoso.com。

    [**應用程式**] 頁面隨即出現，其中顯示貴組織所擁有且可供您使用的所有雲端應用程式。

3. 從 [**應用程式**] 頁面中，選取您想要開始使用的應用程式。

    隨即會開啟應用程式的新頁面，您可以在此登入（如有需要），或開始使用應用程式。

### <a name="access-and-use-the-my-apps-portal-on-an-ios-device"></a>在 iOS 裝置上存取和使用我的應用程式入口網站

從執行 iOS 版本7或更新版本的 iPhone 或 iPad 裝置，觀看並使用**我的應用程式**入口網站。 您也可以安裝我的應用程式行動裝置[應用程式](https://itunes.apple.com/us/app/my-apps-azure-active-directory/id824048653?mt=8)，以在您的 iOS 裝置上存取貴組織的應用程式。

1. 在您的行動裝置上，啟動 web 瀏覽器應用程式，例如 Safari。

2. 移至https://myapps.microsoft.com，或使用組織提供的連結（如果他們將您導向至自訂網頁，例如） https://myapps.microsoft.com/contoso.com。

    [**應用程式**] 頁面隨即出現，其中顯示貴組織所擁有可供您使用的所有雲端應用程式。

3. 從 [**應用程式**] 頁面中，選取您想要開始使用的應用程式。

    隨即會開啟應用程式的新頁面，您可以在此登入（如有需要），或開始使用應用程式。

### <a name="access-and-use-the-my-apps-portal-on-an-android-device"></a>在 Android 裝置上存取和使用我的應用程式入口網站

在 Android 裝置上觀看並使用**我的應用程式**入口網站。

1. 在您的行動裝置上，啟動 web 瀏覽器應用程式，例如 Google Chrome。

2. 移至https://myapps.microsoft.com，或使用組織提供的連結（如果他們將您導向至自訂網頁，例如） https://myapps.microsoft.com/contoso.com。

    [**應用程式**] 頁面隨即出現，其中顯示貴組織所擁有且可供您使用的所有雲端應用程式。

3. 從 [**應用程式**] 頁面中，選取您想要開始使用的應用程式。

    隨即會開啟應用程式的新頁面，您可以在此登入（如有需要），或開始使用應用程式。

## <a name="add-a-new-app-to-the-my-apps-portal"></a>將新的應用程式新增至我的應用程式入口網站

如果您的系統管理員已授與您許可權，您可以將新的應用程式新增至 [**應用程式**] 頁面。

1. 在 [**應用程式**] 頁面中，執行下列其中一項：
    - 如果您是原始我的應用程式體驗，請選取 [**新增應用程式**]，如下所示。

      ![我的應用程式入口網站中的 [新增應用程式] 頁面](media/my-apps-portal/my-apps-portal-add-apps-page.png)

    - 如果您處於更新的我的應用程式體驗，請選取 [**新增自助應用程式**]。

       ![在我的應用程式入口網站的 myapplications.microsoft.com 中新增應用程式頁面](media/my-apps-portal/my-apps-portal-add-app-link.png)

2. 從提供的清單中選取您想要新增的應用程式，然後選取 [**新增**]。

3. 應用程式會新增至 [**應用程式**] 頁面上的清單。

    某些應用程式可能需要系統管理員核准，才能新增。 發生這種情況時，應用程式就不會新增至 [**應用程式**] 頁面，直到系統管理員核准為止。

## <a name="start-a-cloud-based-app"></a>啟動以雲端為基礎的應用程式

您可以從**我的應用程式**入口網站啟動任何可用的雲端型應用程式。 您只會看到您有權使用的應用程式。

- 從 [**應用程式**] 頁面中，選取您想要開始使用的應用程式。

    隨即會開啟應用程式的新頁面，您可以在此登入（如有需要），或開始使用應用程式。

## <a name="activities-in-the-my-apps-portal"></a>我的應用程式入口網站中的活動

進入**我的應用程式**入口網站之後，您可以：

- 將應用程式組織為貴組織所建立和提供的各種類別。 如需詳細資訊，請參閱[在我的應用程式入口網站上存取和使用集合](my-applications-portal-workspaces.md)。

- 檢閱、更新或撤銷已授與應用程式的權限。 如需詳細資訊，請參閱[在我的應用程式入口網站中編輯或撤銷應用程式權限](my-applications-portal-permissions-saved-accounts.md)。

>[!Note]
>如果您未看到任何可用的集合或類別，這表示您的系統管理員尚未設定任何集合或類別或將其與您共用。 請洽詢組織的技術服務人員，以取得其他協助或權限來查看共用的集合。

## <a name="next-steps"></a>後續步驟

進入 [**應用程式**] 頁面之後，您可以：

- [變更設定檔資訊](my-apps-portal-end-user-update-profile.md)

- [查看和更新您的群組相關資訊](my-apps-portal-end-user-groups.md)

- [執行您自己的存取權檢閱](my-apps-portal-end-user-access-reviews.md)
