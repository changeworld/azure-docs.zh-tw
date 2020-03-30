---
title: 登錄為 SSO 配置的 Azure AD 庫應用時遇到問題 |微軟文檔
description: 如何解決為密碼單一登入配置的 Azure AD 庫應用程式的問題。
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: e9fd17d9e066be6a1abff5165436a09b8921184e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "68381304"
---
# <a name="sign-in-problems-with-an-azure-ad-gallery-app-configured-for-sso"></a>為 SSO 配置的 Azure AD 庫應用的登錄問題

訪問面板是一個基於 Web 的門戶。 它使具有 Azure 活動目錄 （Azure AD） 工作或學校帳戶的使用者能夠訪問他們具有許可權的基於雲的應用。 具有 Azure AD 版本的使用者還可以通過訪問面板使用自助服務組和應用管理功能。

訪問面板與 Azure 門戶分開。 使用者不需要 Azure 訂閱才能使用訪問面板。

要在訪問面板中使用基於密碼的單一登入 （SSO），必須在瀏覽器中安裝訪問面板擴展。 當您選擇配置為基於密碼的 SSO 的應用時，擴展會自動下載。

## <a name="browser-requirements-for-access-panel"></a>訪問面板的瀏覽器要求

訪問面板需要支援 JavaScript 並啟用 CSS 的瀏覽器。

以下瀏覽器支援基於密碼的 SSO：

- Windows 7 或更高版本的 Internet 資源管理器 8、9、10 和 11

- 在 Windows 7 或更高版本或 MacOS X 或更高版本中的鉻

- Firefox 26.0 或更高版本在 Windows XP SP2 或更高版本或 Mac OS X 10.6 或更高版本上

>[!NOTE]
>當對瀏覽器延伸的支援添加到 Microsoft Edge 時，基於密碼的 SSO 擴展在 Windows 10 中可供使用。

## <a name="install-the-access-panel-browser-extension"></a>安裝訪問面板瀏覽器延伸

請遵循下列步驟：

1. 在受支援的瀏覽器中打開[訪問面板](https://myapps.microsoft.com)，並在 Azure AD 中以使用者身份登錄。

2. 在訪問面板中選擇啟用密碼 SSO 的應用。

3. 系統提示您時，選擇"**立即安裝**"。

4. 您將被引導到基於您的瀏覽器的下載連結。 選擇 **"添加"** 以安裝瀏覽器延伸。

5. 如果系統提示您，請選擇"**啟用**"或 **"允許**"。

6. 安裝後，重新開機瀏覽器。

7.  登錄到訪問面板，看看是否可以啟動啟用密碼 SSO 的應用。

您也可以通過以下連結直接下載 Chrome 和 Firefox 的擴展：

-   [鉻接入面板擴展](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [火狐接入面板擴展](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="set-up-a-group-policy-for-internet-explorer"></a>設定適用於 Internet Explorer 的群組原則

您可以設置一個群組原則，允許您在使用者的電腦上遠端安裝 Internet Explorer 的訪問面板擴展。

這些是先決條件：

-   必須設置[活動目錄域服務](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx)，並且必須將使用者的電腦加入到域中。

-   您具有編輯群組原則物件 （GPO） 的"編輯設置"許可權。 根據預設，下列安全性群組的成員擁有此權限：網域系統管理員、企業系統管理員及群組原則建立者擁有者。 [深入了解](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx)。

要配置群組原則並將其部署到使用者，請參閱[如何使用群組原則部署 Internet Explorer 的訪問面板擴展](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-group-policy)。

## <a name="troubleshoot-access-panel-in-internet-explorer"></a>在 Internet 資源管理器中排除訪問面板的疑難排解

要訪問診斷工具和配置擴展的說明，請參閱[對 Internet 資源管理器的訪問面板擴展進行故障排除](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-troubleshooting)。

## <a name="configure-password-sso-for-an-azure-ad-gallery-app"></a>為 Azure AD 庫應用配置密碼 SSO

要從 Azure AD 庫配置應用，您需要執行以下操作：

-   從 Azure AD 庫添加應用
-   [為密碼單一登入配置應用](#configure-the-app-for-password-sso)
-   [將使用者指派給應用程式](#assign-users-to-the-app)

### <a name="add-the-app-from-the-azure-ad-gallery"></a>從 Azure AD 庫添加應用

請遵循下列步驟：

1. 打開[Azure 門戶](https://portal.azure.com)，以全域管理員或共同管理員身份登錄。

2. 選擇左側功能窗格頂部**的所有服務**以打開 Azure AD 副檔名。

3. 在篩選器搜索框中鍵入**Azure 活動目錄**，然後選擇**Azure 活動目錄**。

4. 從 Azure AD 功能窗格中選擇**企業應用程式**。

5. 選擇 **"在****企業應用程式"** 窗格的右上角添加。

6. 在"**從庫添加**"部分中，在 **"輸入名稱**"框中鍵入應用的名稱。

7. 選擇要為 SSO 配置的應用。

8. *可選：* 在添加應用之前，可以在 **"名稱"** 框中更改其名稱。

9. 按一下 [新增]****，以新增應用程式。

   短暫延遲後，您將能夠看到應用的配置窗格。

### <a name="configure-the-app-for-password-sso"></a>配置應用以進行密碼 SSO

請遵循下列步驟：

1. 打開[Azure 門戶](https://portal.azure.com/)，以全域管理員或共同管理員身份登錄。

2. 選擇左側功能窗格頂部**的所有服務**以打開 Azure AD 副檔名。

3. 在篩選器搜索框中鍵入**Azure 活動目錄**，然後選擇**Azure 活動目錄**。

4. 在 Azure AD 功能窗格中選擇**企業應用程式**。

5. 選擇 **"所有應用程式**"以查看應用清單。

   > [!NOTE]
   > 如果看不到所需的應用，請使用 **"所有應用程式清單**"頂部的 **"篩選器"** 控制項。 將 **"顯示"** 選項設置為"所有應用程式"。

6. 選擇要為 SSO 配置的應用。

7. 載入應用後，在應用左側的窗格中選擇 **"單一登入**"。

8. 選擇**基於密碼的登錄**模式。

9. 將使用者分配給應用。

10. 您還可以為使用者提供憑據。 （否則，系統將提示使用者在應用啟動時輸入憑據。為此，請選擇使用者的行。 然後選擇 **"更新憑據"** 並輸入其使用者名和密碼。

### <a name="assign-users-to-the-app"></a>將使用者指派給應用程式

要將使用者直接分配給應用，請按照以下步驟操作：

1. 打開[Azure 門戶](https://portal.azure.com/)並作為全域管理員登錄。

2. 選擇左側導航痛動中的所有**服務**以打開 Azure AD 擴展。

3. 在篩選器搜索框中鍵入**Azure 活動目錄**，然後選擇**Azure 活動目錄**。

4. 在 Azure AD 功能窗格中選擇**企業應用程式**。

5. 選擇 **"所有應用程式**"以查看應用程式的清單。

   > [!NOTE]
   > 如果看不到所需的應用，請使用 **"所有應用程式清單**"頂部的 **"篩選器"** 控制項。 將 **"顯示"** 選項設置為"所有應用程式"。

6. 從清單中選擇要將使用者分配到的應用。

7. 載入應用程式後，從左側應用的功能窗格中選擇 **"使用者"和"組**"。

8. 選擇 **"在****使用者和組**"清單的頂部添加以打開"**添加分配"** 窗格。

9. 在 **"添加分配"** 窗格中選擇 **"使用者"和"組**"。

10. 在"**按姓名或電子郵件地址搜索**"框中，鍵入要分配的使用者的全名或電子郵件地址。

11. 將滑鼠懸停在清單中的使用者上。 選擇使用者個人資料照片或徽標旁邊的核取方塊，將該使用者添加到 **"選定"** 清單。

12. *可選：* 要添加其他使用者，請在"**按姓名或電子郵件地址搜索"** 框中鍵入其他名稱或電子郵件地址，然後選擇"核取方塊"將該使用者添加到 **"選定"** 清單。

13. 選擇完使用者後，按一下"**選擇"** 將其添加到分配給應用的使用者和組清單中。

14. *可選：* 按一下"**添加分配"** 窗格中的 **"選擇角色**"以選擇要分配給所選使用者的角色。

15. 選擇 **"分配**"以將應用分配給所選使用者。

    短暫延遲後，使用者將能夠從訪問面板訪問這些應用。

## <a name="request-support"></a>要求支援 
如果在設置 SSO 並分配使用者時收到錯誤訊息，請打開支援票證。 包括盡可能多的以下資訊：

-   相互關聯錯誤 ID
-   UPN (使用者電子郵件地址)
-   TenantID
-   瀏覽器類型
-   發生錯誤的時區和時間/時間範圍
-   Fiddler 追蹤

## <a name="next-steps"></a>後續步驟
[使用應用程式 Proxy 提供單一登入應用程式](application-proxy-configure-single-sign-on-with-kcd.md)
