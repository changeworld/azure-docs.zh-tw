---
title: 轉譯連結和 URL Azure AD Application Proxy | Microsoft Docs
description: 瞭解如何針對使用 Azure AD 應用程式 proxy 發佈的應用程式，重新導向硬式編碼的連結。
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/15/2019
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 08c4020fc03f89b2c583a2458c70e18ecbbe0ba1
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96498059"
---
# <a name="redirect-hard-coded-links-for-apps-published-with-azure-ad-application-proxy"></a>重新導向使用 Azure AD 應用程式 Proxy 發佈之應用程式的硬式編碼連結

Azure AD Application Proxy 讓您的內部部署應用程式可供遠端使用者使用，或讓使用者在其自己的裝置上使用。 不過，有些應用程式是以 HTML 中內嵌的本機連結進行開發。 從遠端使用應用程式時，這些連結無法正常運作。 當您有數個指向彼此的內部部署應用程式時，使用者預期當他們不在辦公室時，連結可持續運作。 

若要確定連結在公司網路內部和外部的運作方式相同，最佳方法就是將您應用程式的外部 URL 設定為與其內部 URL 相同。 使用[自訂網域](application-proxy-configure-custom-domain.md)，將您的外部 URL 設定為包含公司網域名稱，而不是預設的應用程式 Proxy 網域。


如果您無法在租用戶中使用自訂網域，有數個其他選項可提供這項功能。 這些選項全部也都可與自訂網域相容且彼此相容，讓您可以視需要設定自訂網域和其他解決方案。

> [!NOTE]
> 透過 JavaScript 產生的硬式編碼內部 Url 不支援連結轉譯。

**選項1：使用 Microsoft Edge** -只有在您打算建議或要求使用者透過 Microsoft Edge 瀏覽器存取應用程式時，才適用此解決方案。 它將會處理所有已發佈的 URL。 

**選項 2：使用 MyApps 延伸模組** – 這個解決方案會要求使用者安裝用戶端瀏覽器延伸模組，但它將會處理所有已發佈的 URL，而且適用於最熱門的瀏覽器。 

**選項 3：使用連結轉譯設定** – 這是使用者看不到的管理員端設定。 不過，它只會處理 HTML 和 CSS 中的 Url。   

不論您的使用者位於何處，這三個功能都會讓您的連結保持運作。 當您有直接指向內部端點或連接埠的應用程式時，您可以將這些內部 URL 對應至已發佈的外部 Application Proxy URL。 

 
> [!NOTE]
> 最後一個選項僅適用於無法使用自訂網域 (不論何種原因) 讓其應用程式具有相同內部和外部 URL 的租用戶。 在您啟用這項功能之前，請查看您是否適用 [Azure AD Application Proxy 中的自訂網域](application-proxy-configure-custom-domain.md)。 
> 
> 或者，如果您需要透過連結轉譯設定的應用程式為 SharePoint，請參閱[設定 SharePoint 2013 的備用存取對應](/SharePoint/administration/configure-alternate-access-mappings)以取得對應連結的另一種方法。 

 
### <a name="option-1-microsoft-edge-integration"></a>選項1： Microsoft Edge 整合 

您可以使用 Microsoft Edge 來進一步保護您的應用程式和內容。 若要使用此解決方案，您必須要求/建議使用者透過 Microsoft Edge 存取應用程式。 以應用程式 Proxy 發佈的所有內部 Url 都會由 Edge 辨識，並重新導向至對應的外部 URL。 這樣可以確保所有硬式編碼的內部 URL 均會運作，而且，如果使用者前往瀏覽器並直接輸入內部 URL，即便使用者位於遠端，它還是會運作。  

若要深入瞭解，包括如何設定此選項，請參閱 [使用適用于 iOS 和 Android 的 Edge 搭配 Microsoft Intune 檔來管理 web 存取](/mem/intune/apps/manage-microsoft-edge) 。  

### <a name="option-2-myapps-browser-extension"></a>選項 2：MyApps 瀏覽器延伸模組 

使用 MyApps 瀏覽器延伸模組，此延伸模組會辨識出以應用程式 Proxy 發佈的所有內部 URL，並將其重新導向至對應的外部 URL。 這樣可以確保所有硬式編碼的內部 URL 均會運作，而且，如果使用者前往瀏覽器的網址列並直接輸入內部 URL，即便使用者位於遠端，它還是會運作。  

若要使用此功能，使用者必須下載延伸模組並登入。 管理員或使用者不需要任何其他設定。 

若要深入瞭解，包括如何設定此選項，請參閱 [MyApps 瀏覽器延伸](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension) 模組檔。

### <a name="option-3-link-translation-setting"></a>選項 3：連結轉譯設定 

當連結轉譯啟用時，應用程式 Proxy 服務會透過 HTML 和 CSS 搜尋已發佈的內部連結並轉譯它們，讓您的使用者能夠獲得不受干擾的體驗。 使用 MyApps 瀏覽器延伸模組是連結轉譯設定的慣用選項，因為它可為使用者提供更高效能的體驗。

> [!NOTE]
> 如果您使用選項2或3，一次只能啟用其中一個。

## <a name="how-link-translation-works"></a>連結轉譯的運作方式

經過驗證之後，當 proxy 伺服器將應用程式資料傳遞給使用者時，應用程式 Proxy 會掃描應用程式中的硬式編碼連結，並將其取代為其各自發布的外部 Url。

應用程式 Proxy 假設應用程式是以 UTF-8 編碼。 如果不是這種情況，請在 HTTP 回應標頭中指定編碼類型，如下所示 `Content-Type:text/html;charset=utf-8` 。

### <a name="which-links-are-affected"></a>哪些連結會受影響？

連結轉譯功能只會尋找位於應用程式主體的程式碼標籤中的連結。 Application Proxy 有個別功能可轉譯標頭中的 cookie 或 URL。 

內部部署應用程式中有兩種常見的內部連結類型：

- **相對內部連結**，其指向本機檔案結構中的共用資源，例如 `/claims/claims.html`。 這些連結會自動在透過應用程式 Proxy 發佈的應用程式中運作，並且持續運作 (不論是否啟用連結轉譯)。 
- **硬式編碼的內部連結** ，可連至其他內部部署應用程式，例如 `http://expenses` 或已發佈的檔案 `http://expenses/logo.jpg` 。 連結轉譯功能適用于硬式編碼的內部連結，並將其變更為指向遠端使用者需要流覽的外部 Url。

HTML 程式碼標記中應用程式 Proxy 支援連結轉譯的完整屬性清單包括：
*  (href) 
* 音訊 (src) 
* 基底 (href) 
* 按鈕 (formaction) 
* div (資料-背景、樣式、資料-src) 
* 內嵌 (src) 
* 表單 (動作) 
*  (src 的框架) 
* head (設定檔) 
* html (資訊清單) 
* iframe (longdesc、src) 
* img (longdesc、src) 
* 輸入 (formaction、src、value) 
* 連結 (href) 
* menuitem (圖示) 
* 元 (內容) 
* 物件 (保存、資料、程式碼基底) 
*  (src 的腳本) 
* 來源 (src) 
* 追蹤 (src) 
* 影片 (src、海報) 

此外，在 CSS 內也會轉譯 URL 屬性。

### <a name="how-do-apps-link-to-each-other"></a>應用程式如何彼此連結？

每個應用程式都已啟用連結轉譯，以便您控制每個應用程式層級的使用者經驗。 當您想要轉譯「來自」該應用程式的連結 (而非「連到」該應用程式的連結) 時，請開啟應用程式的連結轉譯。 

例如，假設您有三個透過 Application Proxy 發佈且彼此連結的應用程式：Benefits、Expenses 和 Travel。 第四個應用程式 (Feedback) 不是透過 Application Proxy 發佈。

當您啟用 Benefits 應用程式的連結轉譯時，Expenses 和 Travel 的連結會重新導向至這些應用程式的外部 URL，但 Feedback 的連結因為沒有外部 URL 而不會重新導向。 從 Expenses 和 Travel 回到 Benefits 的連結沒有作用，因為這兩個應用程式尚未啟用連結轉譯功能。

![已啟用連結轉譯時從 Benefits 到其他應用程式的連結](./media/application-proxy-configure-hard-coded-link-translation/one_app.png)

### <a name="which-links-arent-translated"></a>哪些連結並未轉譯？

為了改善效能和安全性，並未轉譯有些連結：

- 不在程式碼標籤內的連結。 
- 不在 HTML 或 CSS 中的連結。 
- URL 編碼格式的連結。
- 從其他程式開啟的內部連結。 不會轉譯透過電子郵件或立即訊息傳送的連結，或包含在其他文件中的連結。 使用者必須知道要移至外部 URL。

如果您必須支援下列其中一種情況，請使用相同的內部和外部 URL，而不需進行連結轉譯。  

## <a name="enable-link-translation"></a>啟用連結轉譯

開始使用連結轉譯很簡單，按一下按鈕即可：

1. 以系統管理員身分登入 [Azure 入口網站](https://portal.azure.com)。
2. 移至 **Azure Active Directory**  >  **企業應用** 程式  >  的 **所有應用程式**] > 選取您要 >**應用程式 proxy** 管理的應用程式。
3. 將 [轉譯應用程式主體中的 URL] 切換為 [是]。

   ![選取 [是] 可轉譯應用程式主體中的 URL](./media/application-proxy-configure-hard-coded-link-translation/select_yes.png)
4. 選取 [儲存] 來套用您的變更。

現在，當您的使用者存取此應用程式時，Proxy 會自動掃描已透過您租用戶上的 Application Proxy 發佈的內部 URL。

## <a name="send-feedback"></a>傳送意見反應

我們需要您的協助，讓這項功能可用於您所有的應用程式。 我們在 HTML 和 CSS 中搜尋超過 30 個標籤。 如果您有不在轉譯中的已產生連結範例，請將程式碼片段傳送至 [Application Proxy 意見反應](mailto:aadapfeedback@microsoft.com)。 

## <a name="next-steps"></a>後續步驟
[使用自訂網域搭配 Azure AD 應用程式 Proxy](application-proxy-configure-custom-domain.md) 以具有相同的內部和外部 URL

[設定 SharePoint 2013 的備用存取對應](/SharePoint/administration/configure-alternate-access-mappings)
