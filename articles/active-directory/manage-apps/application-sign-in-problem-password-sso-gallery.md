---
title: 登入為 SSO 設定的 Azure AD 資源庫應用程式時發生問題 |Microsoft Docs
description: 如何針對已設定密碼單一登入的 Azure AD 資源庫應用程式問題進行疑難排解。
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "68381304"
---
# <a name="sign-in-problems-with-an-azure-ad-gallery-app-configured-for-sso"></a>針對 SSO 設定的 Azure AD 資源庫應用程式的登入問題

存取面板是以網頁為基礎的入口網站。 它可讓擁有 Azure Active Directory （Azure AD）工作或學校帳戶的使用者存取他們具有許可權的雲端式應用程式。 具有 Azure AD 版本的使用者也可以透過存取面板使用自助群組和應用程式管理功能。

存取面板與 Azure 入口網站不同。 使用者不需要 Azure 訂用帳戶，即可使用存取面板。

若要在存取面板中使用密碼型單一登入（SSO），存取面板延伸模組必須安裝在您的瀏覽器中。 當您選取針對密碼型 SSO 設定的應用程式時，會自動下載延伸模組。

## <a name="browser-requirements-for-access-panel"></a>存取面板的瀏覽器需求

存取面板需要支援 JavaScript 且已啟用 CSS 的瀏覽器。

下列瀏覽器支援以密碼為基礎的 SSO：

- Windows 7 或更新版本上的 Internet Explorer 8、9、10和11

- Windows 7 或更新版本或 MacOS X 或更新版本上的 Chrome

- Windows XP SP2 或更新版本或 Mac OS X 10.6 或更新版本上的 Firefox 26.0 或更新版本

>[!NOTE]
>當瀏覽器延伸模組的支援已新增至 Microsoft Edge 時，Windows 10 中的 Microsoft Edge 就能使用密碼型 SSO 延伸模組。

## <a name="install-the-access-panel-browser-extension"></a>安裝存取面板的瀏覽器擴充功能

請遵循下列步驟：

1. 在支援的瀏覽器中開啟[存取面板](https://myapps.microsoft.com)，然後在 Azure AD 中以使用者的身分登入。

2. 在存取面板中選取已啟用密碼 SSO 的應用程式。

3. 當系統提示您時，請選取 [**立即安裝**]。

4. 系統會根據您的瀏覽器將您導向至下載連結。 選取 [**新增**] 以安裝瀏覽器延伸模組。

5. 如果出現提示，請選取 [**啟用**] 或 [**允許**]。

6. 安裝之後，請重新開機您的瀏覽器。

7.  登入以存取面板，並查看您是否可以啟動已啟用密碼 SSO 的應用程式。

您也可以透過下列連結直接下載適用于 Chrome 和 Firefox 的延伸模組：

-   [Chrome 存取面板延伸模組](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Firefox 存取面板延伸模組](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="set-up-a-group-policy-for-internet-explorer"></a>設定適用於 Internet Explorer 的群組原則

您可以設定群組原則，讓您從遠端在使用者的電腦上安裝 Internet Explorer 的存取面板延伸模組。

必要條件如下：

-   必須設定[Active Directory Domain Services](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx) ，且您的使用者電腦必須加入您的網域。

-   您擁有編輯群組原則物件（GPO）的「編輯設定」許可權。 根據預設，下列安全性群組的成員擁有此權限：網域系統管理員、企業系統管理員及群組原則建立者擁有者。 [深入了解](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx)。

若要設定群組原則並將它部署至使用者，請參閱[如何使用群組原則部署 Internet Explorer 的存取面板延伸](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-group-policy)模組。

## <a name="troubleshoot-access-panel-in-internet-explorer"></a>疑難排解 Internet Explorer 中的存取面板

若要存取診斷工具和設定擴充功能的指示，請參閱[針對 Internet Explorer 的存取面板延伸模組進行疑難排解](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-troubleshooting)。

## <a name="configure-password-sso-for-an-azure-ad-gallery-app"></a>設定 Azure AD 資源庫應用程式的密碼 SSO

若要從 Azure AD 資源庫設定應用程式，您需要執行下列動作：

-   從 Azure AD 資源庫新增應用程式
-   [設定應用程式的密碼單一登入](#configure-the-app-for-password-sso)
-   [將使用者指派給應用程式](#assign-users-to-the-app)

### <a name="add-the-app-from-the-azure-ad-gallery"></a>從 Azure AD 資源庫新增應用程式

請遵循下列步驟：

1. 開啟[Azure 入口網站](https://portal.azure.com)，並以全域管理員或共同管理員身分登入。

2. 選取左側導覽窗格頂端的 [**所有服務**]，以開啟 [Azure AD] 延伸模組。

3. 在篩選搜尋方塊中輸入**Azure Active Directory** ，然後選取 [ **Azure Active Directory**]。

4. 從 Azure AD 流覽窗格中選取 [**企業應用程式**]。

5. 選取 [**企業應用程式**] 窗格右上角的 [**新增**]。

6. 在 [**從資源庫新增**] 區段的 [**輸入名稱**] 方塊中，輸入應用程式的名稱。

7. 選取您想要為 SSO 設定的應用程式。

8. *選擇性：* 新增應用程式之前，您可以在 [**名稱**] 方塊中變更其名稱。

9. 按一下 [新增]****，以新增應用程式。

   短暫延遲之後，您就可以看到應用程式的 [設定] 窗格。

### <a name="configure-the-app-for-password-sso"></a>設定應用程式的密碼 SSO

請遵循下列步驟：

1. 開啟[Azure 入口網站](https://portal.azure.com/)，並以全域管理員或共同管理員身分登入。

2. 選取左側導覽窗格頂端的 [**所有服務**]，以開啟 [Azure AD] 延伸模組。

3. 在篩選搜尋方塊中輸入**Azure Active Directory** ，然後選取 [ **Azure Active Directory**]。

4. 在 Azure AD 流覽窗格中選取 [**企業應用程式**]。

5. 選取 [**所有應用程式**] 以查看您應用程式的清單。

   > [!NOTE]
   > 如果您沒有看到想要的應用程式，請使用 [**所有應用程式] 清單**頂端的 [**篩選**] 控制項。 將 [**顯示**] 選項設定為 [所有應用程式]。

6. 選取您想要為 SSO 設定的應用程式。

7. 應用程式載入之後，請在應用程式左側的窗格中選取 [**單一登入**]。

8. 選取 [**密碼型登入**模式]。

9. 將使用者指派給應用程式。

10. 您也可以為使用者提供認證。 （否則，系統會提示使用者在應用程式啟動時輸入認證）。若要這麼做，請選取使用者的資料列。 然後選取 [**更新認證**]，並輸入其使用者名稱和密碼。

### <a name="assign-users-to-the-app"></a>將使用者指派給應用程式

若要直接將使用者指派給應用程式，請遵循下列步驟：

1. 開啟[Azure 入口網站](https://portal.azure.com/)，並以全域管理員身分登入。

2. 選取左側導覽困難的 [**所有服務**]，以開啟 [Azure AD] 延伸模組。

3. 在篩選搜尋方塊中輸入**Azure Active Directory** ，然後選取 [ **Azure Active Directory**]。

4. 在 Azure AD 流覽窗格中選取 [**企業應用程式**]。

5. 選取 [**所有應用程式**] 以查看您的應用程式清單。

   > [!NOTE]
   > 如果您沒有看到想要的應用程式，請使用 [**所有應用程式] 清單**頂端的 [**篩選**] 控制項。 將 [**顯示**] 選項設定為 [所有應用程式]。

6. 從清單中，選取您想要指派給使用者的應用程式。

7. 應用程式載入之後，從左側的應用程式流覽窗格中選取 [**使用者和群組**]。

8. 選取 [**使用者和群組**] 清單頂端的 [**新增**]，以開啟 [**新增指派**] 窗格。

9. 選取 [**新增指派**] 窗格中的 [**使用者和群組**]。

10. 在 [**依名稱或電子郵件地址搜尋**] 方塊中，輸入您要指派之使用者的完整名稱或電子郵件地址。

11. 將滑鼠停留在清單中的使用者上方。 選取使用者設定檔相片或標誌旁的核取方塊，將該使用者新增至 [已**選取**] 清單。

12. *選擇性：* 若要新增其他使用者，請在 [**依名稱或電子郵件地址搜尋**] 方塊中輸入另一個名稱或電子郵件地址，然後選取核取方塊，將該使用者新增至 [已**選取**] 清單。

13. 當您完成選取使用者時，按一下 [**選取**] 將其新增至指派給應用程式的使用者和群組清單。

14. *選擇性：* 按一下 [**新增指派**] 窗格中的 [**選取角色**]，選取要指派給您所選取使用者的角色。

15. 選取 [**指派**]，將應用程式指派給選取的使用者。

    短暫延遲之後，使用者將能夠從存取面板存取這些應用程式。

## <a name="request-support"></a>要求支援 
如果您在設定 SSO 並指派使用者時收到錯誤訊息，請開啟支援票證。 盡可能包含下列資訊：

-   相互關聯錯誤 ID
-   UPN (使用者電子郵件地址)
-   TenantID
-   瀏覽器類型
-   發生錯誤時的時區和時間/時間範圍
-   Fiddler 追蹤

## <a name="next-steps"></a>後續步驟
[使用應用程式 Proxy 提供單一登入應用程式](application-proxy-configure-single-sign-on-with-kcd.md)
